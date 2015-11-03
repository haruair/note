---
id: edx-fp101-first-step
title: eDx FP101 First Step
lecture-start: 2015-11-01
lecture-end: 2015-11-03
---

# First Steps

- 교재 [Programming in Haskell](http://www.cs.nott.ac.uk/~pszgmh/book.html) 할인 링크는 디스커스에
- [Hugs 98](https://www.haskell.org/hugs/pages/downloading.htm) 하스켈 인터프리터, [GHC 안쓰는 이유](https://courses.edx.org/courses/course-v1:DelftX+FP101x+3T2015/discussion/forum/i4x-DelftX-FP101x-course-3T2014/threads/56200291d2aca5b29c0007c9)
 - homebrew/boneyard에 있는건 설치 너무 오래 걸림
 - `brew install FranklinChen/tap/hugs --HEAD` [hugs98-plus-Sep2006](https://github.com/FranklinChen/hugs98-plus-Sep2006) 디스커션에서 쓰라는 방법인데 이것도 딱히 빠르진 않은듯. 컴퓨터 성능이 별로라서 그런가...

강의에서 언급되는 GHC는 모두 Hugs를 뜻함.

Karate Kid: 반복숙달의 중요성.

- GHC(Glasgow Haskell Compiler), REPL 물론 여기서는 Hugs 사용.
- GHCi(CLI 도구), `Prelude>`

덧셈, 곱셈, 괄호 연습. Standard Prelude.

    > head [1,2,3]
    1
    > tail [1,2,3,4,5]
    [2,3,4,5]
    # Select the nth element of a list
    > [1,2,3,4,5] !! 2
    3

하스켈에서는 배열보다는 리스트와 함께 고차함수(filter, map, fold) 활용.

    > take 3 [1,2,3,4,5]
    [1,2,3]
    > drop 3 [1,2,3,4,5]
    [4,5]
    > length [1,2,3,4] # time linearly in the length of the list
    4
    > sum [1,2,3,4,5]
    50
    > product [1,2,3,4,5]
    120
    > [1,2,3] ++ [4,5]
    [1,2,3,4,5]
    > reverse [1,2,3,4,5]
    [5,4,3,2,1]

## 함수 어플리케이션 function application
수학에서 빈칸은 곱을 의미하고 함수에 괄호가 필요하지만 하스켈은 다음과 같음

    f(a,b) + c d # 수학
    f a b + c*d # 하스켈
    f a + b
    = (f a) + b != f (a + b)

함수를 하스켈로 하면,

    f(x) = f x
    f(x,y) = f x y
    f(g(x)) = f (g x)
    f(x, g(y)) = f x (g y)
    f(x)g(y) = f x * g y

## 하스켈 스크립트

- 프로그램 < 스크립트
- 표준 라이브러리, 직접 작성한 함수
- 확장자 `.hs`
- infix operator x \`f\` y == f x y

REPL에서 `:reload` 로 다시 불러옴

    hugs 01-test.hs
    > :reload

팩토리얼, 2곱, 4곱, 평균 예제. `01-test.hs`

## 네이밍 컨벤션

- 함수는 소문자로 시작
- 타입은 대문자로 시작
- 인자 목록은 접미사 s (헝가리안 느낌적 느낌)
 - xs, ns, nss <<리스트의 리스트

## 레이아웃 규칙

- 들여쓰기/빈칸에 민감, 같은 줄에서 시작
- 암시적인 묶기가 선호됨

    -- 암시적 묶기
    a = b + c
        where
          b = 1
          c = 2
    d = a * 2

    -- 명시적 묶기
    a = b + c
        where
          {b = 1;
           c = 2}
    d = a * 2

## GHCi 명령어
`:load name`, `:reload`, `:edit name`, `:edit`, `:type expr`, `:?`, `:quit`

----

# 하스켈 연습
사칙연산, True/False, &&과 ||, not

    > 2 + 2   -- infix
    > (+) 2 2 -- prefix

대소문자 구분함

정의되지 않은 경우, `not in scope`

    > True || True && False
    False -- (||) 보다 (&&)이 우선

문자열 합치기 (++)

    > lenght "Hello"
    5
    > head "Hello"
    'H'
    > tail "Hello"
    "ello"
    > last "Hello"
    'o'
    > init "Hello"
    "Hell"
    > reverse "Hello"
    "olleH"
    > null "Hello"
    False

    > head ""
    -- 에러, 문자열의 첫번째 문자가 없음
    > "Hello
    -- lexical error
    > not "Hello"
    -- type error

타입은 `:type`, `:t`로 확인 가능

    > :t True
    True :: Bool
    > :t "Hello"
    "Hello" :: String
    -- 강의엔 [Char]로 나오는데 왜 다르지? String의 정의가 [Char]인 것인지
    > head "Hello"
    'H'
    > :t head "Hello"
    head "Hello" :: Char

    > :t not
    not :: Bool -> Bool
    -- 불을 받아서 불을 반환, not "Hello"는 에러

    > :t length
    length :: [a] -> Int
    -- a는 타입 변수 type variable, 제네릭 같은 역할

이건 좀 특이했음.

    > length [length, head]

둘 다 함수니까? 문제 없이 답은 2.

    > length [head, tail]

에러 발생. 리스트 내에 타입이 다르기 때문. *리스트는 안에 담긴 모든 항목이 동일한 타입이어야 함*. `:type`으로 확인할 것. 그래서 아래는 에러가 발생하지 않는다.

    > length [head, last]
    > length [tail, init]

그럼 왜 [length, head]가 가능한가?

    > :t [length]
    [length] :: [[a] -> Int]
    > :t [head]
    [head] :: [[a] -> a]
    > :t [length, head]
    [length,head] :: [[Int] -> Int]

    > (head [length]) "Hello"
    -- length "Hello"
    5

리스트는 동일한 타입만 가능하지만 튜플(tuple)은 다른 타입도 가능. 대신 길이가 고정.

튜플의 타입은 각 요소의 타입을 반영.

    > (True, "Hello", [1,2])
    > :t (True, "Hello", [1,2])
    (True,"Hello",[1,2]) :: Num a => (Bool,[Char],[a])

튜플은 주로 짝으로 많이 사용.

    > fst (1, "Hello")
    1
    > snd (1, "Hello")
    "Hello"
    > fst (1, 2, 3)
    ERROR
    > :t fst
    fst :: (a,b) -> a
    > :t snd
    snd :: (a,b) -> b
    > fst (snd (1, (2, 3)))
    2

----

Lab에 페이지가 많길래 레퍼런스를 다 보는건가 했더니 Groovy, F# 같은 다른 언어로도 코스가 진행되나보다. 하스켈을 배워보기로 했으니 이쪽으로 먼저 보고 나중에 F#으로 한번 더 봐야겠다.

## Homework
다른 연습 문제는 크게 어려운 부분이 없는데 quick sort 구현 문제가 나온다. 정렬 알고리즘도 공부를 좀 해둬야겠다.

- [퀵 정렬](https://ko.wikipedia.org/wiki/%ED%80%B5_%EC%A0%95%EB%A0%AC)

