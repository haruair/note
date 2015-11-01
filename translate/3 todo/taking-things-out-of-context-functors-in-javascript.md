---
layout: post
title: "Taking Things Out of Context: Functors in JavaScript"
category: javascript
tags: [functional programming, javascript, introduction, tuts]
http://mattfield.github.io/javascript/2013/07/28/taking-things-out-of-context-functors-in-javascript/
---

## 값과 맥락

다음의 값을 가져올 수 있다:

{% highlight javascript %}
5
{% endhighlight %}

그리고 이 값을 함수에 적용할 수 있다:
    
{% highlight javascript %}
function addOne(a) { return a + 1; };
addOne(5);
//=> 6
{% endhighlight %}

상당히 직설적이다. 여기서 알아야 하는 점은 입력한 값이 내부의 "맥락"에서 존재한다는 점이다. 맥락을 쉽게 생각하는 방법은 상자 안에 값을 넣었다고 생각하는 것이다. 그래서 값에 대한 맥락을 바꾼다면, 다시 말해 이 값을 배열로 감싸 `addOne` 함수에 다시 실행한다면 어떻게 될까?

{% highlight javascript %}
addOne([5]);
//=> 51
{% endhighlight %}

뭐라고?

함수가 반환하는 결과가 다른 이유는 함수에 값을 적용할 때, 값의 맥락이 다르기 때문이다. `addOne` 함수는 이 함수가 의도한 맥락에서 값을 제공하면 정상적으로 동작한다. 하지만 그 맥락을 바꾸면 모든 것이 잘못되고 만다. 여전히 같은 데이터 타입을 가진 같은 값을 다루고 있지만 이 둘은 근본적으로 달라졌다. 이 맥락의 차이가 현재 우리에게 놓인 상황이다.

이 문제를 어떻게 해결해야 할까?

## 그것을 맥락에서 분리해라

값을 맥락으로 감싸게 될 때, 간단히 일반적인 함수에 적용하는 것으로는 고장날 수 밖에 없다. 이런 경우에는 그 함수의 맥락 안에서 어떻게 값을 얻고 그 값을 사용할 수 있는가가 필요하다. 위의 배열 예제 인스턴스에서, 사실 아주 간단하게 `addOne` 함수를 배열에서도 제대로 활용할 수 있는 방법이 있다. [Lodash의 `map`](http://lodash.com/docs#map)을 이용하는 것이다:

{% highlight javascript %}
_.map([5, 6, 7], addOne);
//=> [6, 7, 8]
{% endhighlight %}

위 코드가 아주 익숙할 것이다. 하지만 실제로 그 속에서는 무슨 일이 일어나고 있을까?

`_.map`이 여기서 하는 일은 *들어 올리기 lifting*라고 한다. 여기서는 `addOne` 함수를 배열로 들어 올려, 각각의 배열 인덱스에 있는 값을 이용해 호출한 후, 그 결과를 배열로 돌려준다:

{% highlight javascript %}
_.map([5, 6, 7], addOne);
//=> [addOne(5), addOne(6), addOne(7)]
{% endhighlight %}

`_.map`은 값을 *그 맥락의 바깥*으로 가져와 동작하게 한 후, 작업이 끝나면 다시 값을 박스에 넣어 앞서 사용했던 원래의 맥락으로 돌려 놓는다.

그래서 `_.map`은 값이 배열로 감싸져 있을 때 어떻게 함수를 적용하는지 알고 있는 것이다. (물론 객체도 포함된다.) 그래서 여기서 질문은 이렇다. 이것보다 더 추상적인 방법은 없을까? 그냥 배열이 아니라 다른 데이터 타입의 반복 iteration에서 같은 메소드를 사용할 수 있지 않을까?

확실히 할 수 있다. 하지만 하나 주의할 점이 있다.

Underscore/Lodash의 `map` 함수는 이제 만들 인터페이스를 구현하면 다음으로 필요없게 될 것이다. 우린 유연함이 더 필요하다. 새로 생성할 객체를 위한 `map` 함수를 직접 정의하고 객체가 하길 원하는 일에 맞춰 다르게 구현할 필요가 있다. 여기서는 [typeclasses](https://github.com/loop-recur/typeclasses)라는 작은 컬렉션을 위한 간단한 추상화 라이브러리를 활용해 이 작업을 더 쉽게 진행할 것이다.

## Functors

자, 이제 심각하게 시작하도록 한다. 안전벨트를 단단히 매라.

흔하게 객체의 생성자를 만들면서 시작한다. 값을 위한 새로운 컨테이너를 다음과 같이 작성한다:

{% highlight javascript %}
var MyObj = function(val){
  this.val = val;
};
{% endhighlight %}

이 코드는 사실 함수형 프로그래밍에서 어떻게 다른 타입을 객체로 다루는지에 대한 좋은 표현이다. 객체를 맥락으로, 또는 값을 위한 컨테이너로 사용한다.

여기서 가능하게 하고 싶은 일은 이 객체와 연결하고 이 컨텐츠와 함께 함수를 실행하려고 한다. 그래서 이 인터페이스를 통해 앞서 본 `_.map`과 같이
함수를 들어올려 객체와 함께 동작하게 하고 각각의 값에 실행하는데 촛점을 맞춘다:

{% highlight javascript %}
// MyObj와 연결
map(addOne, MyObj(5));

// 이렇게 처리되야 한다
MyObj(addOne(5));
{% endhighlight %}

What we need to do is define our own `fmap` on `MyObj`. We need a **Functor**. Don't worry if you're not 100% certain what's 
going on here, we'll get right to it in a minute:

{% highlight javascript %}
Functor(MyObj, {
  fmap: function(f){
    return MyObj(f(this.val));
  }
};

fmap(addOne, MyObj(5));
//=> 6
{% endhighlight %}

What we've just done is define a **Functor**. A Functor is a [typeclass](http://learnyouahaskell.com/types-and-typeclasses#typeclasses-101), a sort 
of interface that defines a behaviour. If we want to get all Haskell up in here, this is the typeclass definition:

{% highlight haskell %}
class Functor f where
  fmap :: (a -> b) -> fa -> fb
{% endhighlight %}

All we're saying here is that to make a Functor from any data type, we just need to define how `fmap` will work with it. 

Congratulations! Now you know what a Functor is, we can talk about how we can put them to good use.

## Use You a Functor for Great Good!

So I'm sure the question on your lips right now is: how is any of this mumbo-jumbo useful? Turns out there 
are a number of practical applications for functors. Let's start with one we'll call `Maybe`:

### The Maybe Functor

{% highlight javascript %}
var Maybe = Constructor(function(val){
  this.val = val;
});

Functor(Maybe, {
  fmap: function(f) {
    return this.val ? Maybe(f(this.val)) : Maybe(null);
  }
});

fmap(addOne, Maybe(3));
//=> Maybe(4)

fmap(addOne, Maybe(null));
//=> Maybe(null)
{% endhighlight %}

`Maybe` says that whatever context our function is mapping over might contain a value or it might not. 
If it does contain a value then run the function against it, but if it doesn't then don't run the function at all and return the `Maybe` back. 
This is a really handy little functor as it's basically an abstracted `null` check that we can map over!

Here's a real-world example. We're writing a user settings page for a client-side application. 
We have data for the currently logged-in user available to us, which we're using
to populate our settings page. However, our user felt a bit dubious about providing us with his 
address when he signed up for his account, meaning we have no data object for it. How do we deal with 
this when everything gets passed to our page for rendering?

This is where our friend `Maybe` can help:

{% highlight javascript %}
// Grab current user data
var currentUser = App.get(current_user);

// Before using our Maybe functor, we might be passing 
// a non-existent value to updateField causing our 
// whole app to blow up and barf an error at us
updateField(currentUser.address);

// Or, we can map our updateField function over
// our Maybe-wrapped value, giving us a null check 
// on our value! Not to mention a spot of 
// dynamic type-checking, too.
fmap(updateField, Maybe(currentUser.address));
{% endhighlight %}

...................................

## The Either Functor

Here's another functor, `Either`:

{% highlight javascript %}
var Either = Constructor(function(left, right){
  this.left = left;
  this.right = right;
};

Functor(Either, {
  fmap: function(f){
    return this.right ? 
           Either(this.left, f(this.right)) :
           Either(f(this.left), this.right);
  }
});

fmap(addOne, Either(5, 6));
//=> Either(5, 7);

fmap(addOne, Either(1, null));
//=> Either(2, null);
{% endhighlight %}

`Either` is an abstraction that provides you with defaults. When we try to map `addOne` over our `Either`, it will apply our function 
to the right value if it's present, or against the left value if it isn't.

How is this useful? Using our previous example, instead of just checking the existance of our value we can use our `Either` functor to 
provide our app with a default value. That way, our address field wouldn't be blank in the absence of data, but be
pre-filled with some default instead:

{% highlight javascript %}
fmap(updateField, 
     Either({ address: "The Post-Apocalyptic Land of Ooo" }, currentUser.address);
{% endhighlight %}

...................................

## Conclusion
Functors aren't these mystical programming entities resigned to exist only in the brains of Computer Science 
graduates, ivory-tower academics and Haskell. They are extremely useful constructs that make operating on data 
in a functional way much easier. Hopefully this post has given you some insight into how to take your 
JavaScript to the next level: use you a Functor!

NB This post and entire blog is [open-sourced on GitHub](https://github.com/mattfield/mattfield.github.io/)

## References
* [Functors, Applicatives and Monads in Pictures](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)
* ["Hey Underscore, You're Doing It Wrong!"](https://www.youtube.com/watch?v=m3svKOdZijA) - a talk by Brian Lonsdorf at [HTML5DevConf 2013](http://html5devconf.com/)
* ["Learn You a Haskell for Great Good!"](http://learnyouahaskell.com)
