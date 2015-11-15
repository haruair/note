---
id: edx-fp101-types-and-classes
title: eDx FP101 Types and Classes
lecture-start: 2015-11-03
lecture-end:
---

# Types and Classes
음식 피라미드, 어떻게 비슷한 내용을 묶을 것인가.

## Type

- A _type_ is a name for a collection of related values.
- `Bool`은 타입, 값은 `True`, `False`
- Type Error는 이 타입이 맞지 않을 때 발생, compile time
- static, dynamic, non typing
- `e :: t`
- type inference

Every well formed expression has a type, which can be automatically calculated at compile time using a process called _type inference_.

    > not False
    True
    > :t not False
    not False :: Bool -- type soundness

## 기본 타입
`Bool`, `Char`, `String`(lists of chars), `Int`(fixed-precision, 32/64bit), `Integer`(arbitrary-precision), `Float`

## List 타입
같은 타입 목록, 타입 t를 요소로 갖는 리스트 `[t]`, 길이 제한이 없음

    [False, True, False] :: [Bool]
    [['a'],['b','c']] :: [[Char]]

## Tuple 타입
다양한 타입을 요소로 사용할 수 있지만 길이가 정의됨.

    (False,'a') :: (Bool,Char)
    (True,['a','b']) :: (Bool,[Char])

## Function 타입
function은 한 타입의 값에서 다른 타입의 값으로 mapping 하는 것

    not :: Bool -> Bool
    isDigit :: Char -> Bool


