# 07 장: 힌들리-밀너와 나

## 당신은 무슨 타입인가요?

## What's Your Type?

당신이 함수형 세상에 처음 왔다면 머지않아 타입 명세에 깊이 빠지게 될 거에요. 타입은 다른 배경을 가진 사람들이 서로 간단명료하고 효과적으로 소통할 수 있게 해주는 메타 언이입니다. 대부분 우리가 이 장에서 다루게 될 "힌들리-밀너(Hindley-Milner, HM)"이라고 불리는 시스템을 이용해 타입을 적을 것입니다.
If you're new to the functional world, it won't be long before you find yourself knee deep in type signatures. Types are the meta language that enables people from all different backgrounds to communicate succinctly and effectively. For the most part, they're written with a system called "Hindley-Milner", which we'll be examining together in this chapter.

순수 함수를 사용할 때 타입 명세는 영어가 조금도 다루지 못하는 것을 표현할 힘을 가지고 있어요. 이 명세들은 함수의 내밀한 비밀을 당신 귀에 속삭여 줄 것입니다. 단지 간결한 한 줄로 행동과 의도를 알려줄 거에요. 또 "공짜 정리"를 도출 할 수도 있습니다. 명시적으로 적지 않아도 될 만큼 타입은 추론될 수도 있어요. 아주 세밀하게 조절될 수도 있고 일반적이고 추상적으로 남을 수도 있어요. 컴파일 타임 체크에 유용할 뿐만 아니라 문서화에도 사용할 수 있어요. 따라서 타입 명세는 당신이 처음에 생각하는 것 보다 더 함수형 프로그래밍에서 중요한 부분을 차지하고 있습니다.
When working with pure functions, type signatures have an expressive power to which the English language cannot hold a candle. These signatures whisper in your ear the intimate secrets of a function. In a single, compact line, they expose behaviour and intention. We can derive "free theorems" from them. Types can be inferred so there's no need for explicit type annotations. They can be tuned to fine point precision or left general and abstract. They are not only useful for compile time checks, but also turn out to be the best possible documentation available. Type signatures thus play an important part in functional programming - much more than you might first expect.

자바스크립트는 동적 언어지만 이것이 전혀 타입을 고려하지 않는 다는 것을 의미하지는 않아요. 우리는 문자열과 숫자, 불리언들을 사용합니다. 이것은 그저 언어 레벨의 통합이 없어서 이 정보들을 우리가 기억하고 있어야 된다는 것을 의미해요. 그러나 우리는 문서화에서 명세를 사용하고 있기 때문에 우리의 목적을 위해 주석을 이용할 수 있어요.
JavaScript is a dynamic language, but that does not mean we avoid types all together. We're still working with strings, numbers, booleans, and so on. It's just that there isn't any language level integration so we hold this information in our heads. Not to worry, since we're using signatures for documentation, we can use comments to serve our purpose.

[Flow](https://flow.org/)나 [TypeScript](https://www.typescriptlang.org/)같이 자바스크립트에서 이용할 수 있는 타입 검사 도구들도 있어요. 이 책의 목적은 여러 FP 언어에 걸쳐 사용할 수 있는 일반적인 타입 시스템을 갖추는 것입니다.
There are type checking tools available for JavaScript such as [Flow](https://flow.org/) or the typed dialect, [TypeScript](https://www.typescriptlang.org/). The aim of this book is to equip one with the tools to write functional code so we'll stick with the standard type system used across FP languages.

## 비밀의 이야기

## Tales from the Cryptic

낡은 수학 책에서, 광활한 햐얀 종이의 바다에서, 일상의 토요일 아침 블로그 글에서, 소스 코드의 밑바닥에서 우리는 힌들리-밀너 타입 명세를 볼 수 있어요. 이 시스템은 매우 단순하지만 빠른 설명과 이 작은 언어를 배우기 위한 연습들을 보장합니다.
From the dusty pages of math books, across the vast sea of white papers, amongst casual Saturday morning blog posts, down into the source code itself, we find Hindley-Milner type signatures. The system is quite simple, but warrants a quick explanation and some practice to fully absorb the little language.

```js
// capitalize :: String -> String
const capitalize = (s) => toUpperCase(head(s)) + toLowerCase(tail(s));

capitalize("smurf"); // 'Smurf'
```

위의 `capitalize`는 `String`을 받아서 `String`을 반환합니다. 구현은 신경쓰지 마세요. 우리가 관심있는 것은 타입 명세입니다.
Here, `capitalize` takes a `String` and returns a `String`. Never mind the implementation, it's the type signature we're interested in.

HM에서 함수는 `a -> b`로 쓰여집니다. 여기서 `a`와 `b`는 어떤 타입을 나타내는 변수입니다. 따라서 `capitalize`의 명세는 "`String`에서 `String`으로 가는 함수"라고 읽을 수 있어요. 또는 `String`을 입력으로 받고 `String`을 출력으로 반환한다고 볼 수도 있습니다.
In HM, functions are written as `a -> b` where `a` and `b` are variables for any type. So the signatures for `capitalize` can be read as "a function from `String` to `String`". In other words, it takes a `String` as its input and returns a `String` as its output.

다른 함수 명세도 봅시다.
Let's look at some more function signatures:

```js
// strLength :: String -> Number
const strLength = (s) => s.length;

// join :: String -> [String] -> String
const join = curry((what, xs) => xs.join(what));

// match :: Regex -> String -> [String]
const match = curry((reg, s) => s.match(reg));

// replace :: Regex -> String -> String -> String
const replace = curry((reg, sub, s) => s.replace(reg, sub));
```

`strLength`도 앞과 비슷합니다. `String`을 받아서 `Number`를 반환해요.
`strLength` is the same idea as before: we take a `String` and return you a `Number`.

다른 것들은 첫눈에는 헷갈릴 수 있어요. 완전히 이해하지 않아도 언제나 마지막 타입을 반환값의 타입으로 생각할 수 있어요. 따라서 `match`는 `Regex`과 `String`을 받아서 `[String]`을 반환한다고 해석할 수 있어요. 하지만 제가 시간을 들여 설명하고 싶은 흥미로운 일도 일어나고 있어요.
The others might perplex you at first glance. Without fully understanding the details, you could always just view the last type as the return value. So for `match` you can interpret as: It takes a `Regex` and a `String` and returns you `[String]`. But an interesting thing is going on here that I'd like to take a moment to explain if I may.

`match`의 명세를 다음과 같이 짝지을 수도 있어요.
For `match` we are free to group the signature like so:

```js
// match :: Regex -> (String -> [String])
const match = curry((reg, s) => s.match(reg));
```

뒷부분을 괄호로 묶는것은 더 많은 정보를 줍니. 이제 함수를 `Regex`를 받아서 `String`에서 `[String]`으로 가는 함수를 반환하는 것으로 보입니다. 커링 때문에 이것은 사실 다음 케이스입니다. `Regex`를 주면 우리는 `String`을 받기를 기다리는 함수를 얻습니다. 당연하게도 이런식으로 생각할 필요는 없어요. 하지만 왜 마지막 타입이 반환 타입인지를 이해하고 있는 것은 좋습니다.
Ah yes, grouping the last part in parenthesis reveals more information. Now it is seen as a function that takes a `Regex` and returns us a function from `String` to `[String]`. Because of currying, this is indeed the case: give it a `Regex` and we get a function back waiting for its `String` argument. Of course, we don't have to think of it this way, but it is good to understand why the last type is the one returned.

```js
// match :: Regex -> (String -> [String])
// onHoliday :: String -> [String]
const onHoliday = match(/holiday/gi);
```

모든 인자들이 타입의 가장 앞의 하나를 pop합니다. `onHoliday`는 이미 `Regex`를 받은 `match`입니다.
Each argument pops one type off the front of the signature. `onHoliday` is `match` that already has a `Regex`.

```js
// replace :: Regex -> (String -> (String -> String))
const replace = curry((reg, sub, s) => s.replace(reg, sub));
```

`replace`의 모든 괄호처럼 추가적인 명시는 약간 지저분하고 불필요하게 보이기 때문에 우리는 그저 생략합니다. 모든 인자들을 한번에 주기로 정한다면 `replace`를 단순히 `Regex`와 `String`과 또 다른 `String`을 받아서 `String`을 반환하는 함수로 볼 수 있답니다.
As you can see with the full parenthesis on `replace`, the extra notation can get a little noisy and redundant so we simply omit them. We can give all the arguments at once if we choose so it's easier to just think of it as: `replace` takes a `Regex`, a `String`, another `String` and returns you a `String`.

여기 몇가지 더 있어요.
A few last things here:

```js
// id :: a -> a
const id = (x) => x;

// map :: (a -> b) -> [a] -> [b]
const map = curry((f, xs) => xs.map(f));
```

`id` 함수는 오래된 `a` 타입을 받아서 같은 타입 `a`의 어떤 것을 반환합니다. 우리는 코드 안에서 처럼 타입을 나타내는 변수도 사용할 수 있어요. `a`나 `b` 같은 변수명은 컨벤션이지만 그들은 임의적이고 당신이 원하는 다른 이름으로 바꿀 수 있어요. 만약 같은 변수라면 같은 타입입니다. 이것은 중요한 규칙이기 때문에 다시 한번 살펴봅시다. `a -> b`는 어떤 타입 `a`를 어떤 타입 `b`로 보내지만 `a -> a`는 서로 같은 타입임을 뜻합니다. 예를들어 `id`는 `String -> String`이나 `Number -> Number`가 될 수 있지만 `String -> Bool`은 될 수 없어요.
The `id` function takes any old type `a` and returns something of the same type `a`. We're able to use variables in types just like in code. Variable names like `a` and `b` are convention, but they are arbitrary and can be replaced with whatever name you'd like. If they are the same variable, they have to be the same type. That's an important rule so let's reiterate: `a -> b` can be any type `a` to any type `b`, but `a -> a` means it has to be the same type. For example, `id` may be `String -> String` or `Number -> Number`, but not `String -> Bool`.

`map`도 마찬가지로 타입 변수를 사용하지만 이번에는 `a`와 같은 타입일수도, 다를 수도 있는 `b`를 소개합니다. `map`을 어떤 타입 `a`를 받아서 같거나 다를 수도 있는 타입 `b`를 반환하는 함수를 받은 다음에 `a`의 배열을 받아서 `b`의 배열을 반환하는 함수로 볼 수 있어요.
`map` similarly uses type variables, but this time we introduce `b` which may or may not be the same type as `a`. We can read it as: `map` takes a function from any type `a` to the same or different type `b`, then takes an array of `a`'s and results in an array of `b`'s.

당신이 이 타입 명세의 풍부한 표현력의 아름다움을 극복했지를 바래요. 이것은 함수가 말그대로 무엇인지를 말해요. 이것은 `a`에서 `b`로 가는 함수와 `a`의 배열을 받아서 `b`의 배열을 주지요. 유일하게 현명한 할 일은 피투성이 함수(...)를 각각의 `a`에 적용하는 거지요. 이 밖의 다르 것은 철면피의 거짓말일거에요.
Hopefully, you've been overcome by the expressive beauty in this type signature. It literally tells us what the function does almost word for word. It's given a function from `a` to `b`, an array of `a`, and it delivers us an array of `b`. The only sensible thing for it to do is call the bloody function on each `a`. Anything else would be a bold face lie.

타입과 그 구현에 대해 생각할 수 있게 되는 것은 당신을 함수형 세상으로 데려다 줄 능력입니다. 논문, 블로그, 문서 등을 좀 더 쉽게 이해하게 될 뿐만 아니라 명세 그 자체만으로 그들의 기능에 대해 알려줄 것입니다. 수월하게 읽으려면 연습이 필요하겠지만 포기하지 않는다면 망할 매뉴얼을 읽지 않고도 정보 뭉치를 당신에게 줄 거에요.
Being able to reason about types and their implications is a skill that will take you far in the functional world. Not only will papers, blogs, docs, etc, become more digestible, but the signature itself will practically lecture you on its functionality. It takes practice to become a fluent reader, but if you stick with it, heaps of information will become available to you sans RTFMing.

다음의 당신 스스로 해석할 수 있는지 한 번 연습해 보세요.
Here's a few more just to see if you can decipher them on your own.

```js
// head :: [a] -> a
const head = (xs) => xs[0];

// filter :: (a -> Bool) -> [a] -> [a]
const filter = curry((f, xs) => xs.filter(f));

// reduce :: ((b, a) -> b) -> b -> [a] -> b
const reduce = curry((f, x, xs) => xs.reduce(f, x));
```

`reduce`는 아마 이 중에 가장 표현력이 있을 거에요. 하지만 약간 어려우니까 힘들더라고 당신이 부족하다고 생각하지 마세요. 궁금하신 분들을 위해 설명해 보겠지만 당신 스스로 해보는 것이 더 도움이 될겁니다.
`reduce` is perhaps, the most expressive of all. It's a tricky one, however, so don't feel inadequate should you struggle with it. For the curious, I'll try to explain in English though working through the signature on your own is much more instructive.

흠.. 여기 아무일도 일어나지 않군요... 타입 명세를 보면 첫번째 인자는 `b`와 `a`를 받는 함수입니다. `a`와 `b`는 어디서 얻을까요? 명세의 다음 인자는 `b`와 `a`의 배열이네요. `b`와 각각의 `a`가 먹여진다는 것을 가정할 수 있을 거에요. 그리고 함수의 반환값은 `b`니까 함수로 먹여진 마지막 마술이 결과값이라고 생각할 수 있을 거에요. `reduce`가 무엇을 하는지 알면 위의 설명이 정확하다는 것을 알게 될 거에요.
Ahem, here goes nothing....looking at the signature, we see the first argument is a function that expects `b` and `a`, and produces a `b`. Where might it get these `a`s and `b`s? Well, the following arguments in the signature are a `b` and an array of `a`s so we can only assume that the `b` and each of those `a`s will be fed in. We also see that the result of the function is a `b` so the thinking here is our final incantation of the passed in function will be our output value. Knowing what reduce does, we can state that the above investigation is accurate.

## 확률을 좁히기

## Narrowing the Possibility

타입 변수가 도입되면 **[parametricity](https://en.wikipedia.org/wiki/Parametricity)**라고 불리는 특이한 속성이 등장합니다. 이 속성은 함수가 **모든 타입에 걸쳐 일관된 방식으로 작동할 것**이라고 말합니다. 한번 알아봅시다.
Once a type variable is introduced, there emerges a curious property called _[parametricity](https://en.wikipedia.org/wiki/Parametricity)_. This property states that a function will _act on all types in a uniform manner_. Let's investigate:

```js
// head :: [a] -> a
```

`head`를 보면 `[a]`를 받아서 `a`를 반환합니다. `array` 타입 이외의 정보는 없고 따라서 이 함수는 배열에만 작용하는 것으로 한정됩니다. `a`에 대해 아무것도 모르는데 `a`를 가지고 무엇을 할 수 있겠어요? 다른말로 `a`는 **특정한** 타입이 될 수 없고 따라서 **어떤** 타입도 될 수 있습니다. 그리고 이 함수는 생각할 수 있는 **모든** 타입 타입에 일관적으로 작동한다는 것을 의미합니다. 이것이 **parametricity**가 의미하는 모든 것입니다. 구현을 추측해보자면 유일하게 합리적인 가정은 배열의 첫번째 또는 마지막 또는 임의의 원소를 반환한다는 것입니다. `head`라는 이름이 귀뜸해주네요.
Looking at `head`, we see that it takes `[a]` to `a`. Besides the concrete type `array`, it has no other information available and, therefore, its functionality is limited to working on the array alone. What could it possibly do with the variable `a` if it knows nothing about it? In other words, `a` says it cannot be a _specific_ type, which means it can be _any_ type, which leaves us with a function that must work uniformly for _every_ conceivable type. This is what _parametricity_ is all about. Guessing at the implementation, the only reasonable assumptions are that it takes the first, last, or a random element from that array. The name `head` should tip us off.

다른 것을 한번 봅시다.
Here's another one:

```js
// reverse :: [a] -> [a]
```

타입 명세만으로 `reverse`가 무엇을 할 수 있을까요? 다시한번 특정한 `a`에 대해서만 적용되면 안되고 `a`를 다른 타입으로 바꿀 수 없어요. 또 `b`를 도입할 수도 없습니다. 정렬할 수 있을까요? 흠.. 가능한 모든 타입에 대해 어떻게 정렬하는지 충분한 정보가 없기 때문에 못합니다. 재배열을 할 수 있을까요? 네, 할 수 있을 것 같아요. 하지만 마찬가지로 예상 가능하게 작동해야합니다. 중요한 점은 모든 상황에서 이것의 polymorphic type에 의해 해이동이 되게 많이 제약된다는 것입니다.
From the type signature alone, what could `reverse` possibly be up to? Again, it cannot do anything specific to `a`. It cannot change `a` to a different type or we'd introduce a `b`. Can it sort? Well, no, it wouldn't have enough information to sort every possible type. Can it re-arrange? Yes, I suppose it can do that, but it has to do so in exactly the same predictable way. Another possibility is that it may decide to remove or duplicate an element. In any case, the point is, the possible behaviour is massively narrowed by its polymorphic type.

이렇게 가능성을 줄이는 것은 [Hoogle](https://hoogle.haskell.org/) 같이 타입 명세로 함수를 검색하는 검색 엔진을 가능하게 합니다. 실제로 타입에 타이트하게 들어있는 정보는 매우 강력합니다.
This narrowing of possibility allows us to use type signature search engines like [Hoogle](https://hoogle.haskell.org/) to find a function we're after. The information packed tightly into a signature is quite powerful indeed.

## 공짜로 정리 얻기

## Free as in Theorem

가능한 구현을 추측하는 것 이외에도 이런 종류의 사고는 **공짜 정리**를 줍니다. 다음은 [Wadler's paper on the subject](http://ttic.uchicago.edu/~dreyer/course/papers/wadler.pdf)에 나온 몇가지 예제입니다.
Besides deducing implementation possibilities, this sort of reasoning gains us _free theorems_. What follows are a few random example theorems lifted directly from [Wadler's paper on the subject](http://ttic.uchicago.edu/~dreyer/course/papers/wadler.pdf).

```js
// head :: [a] -> a
compose(f, head) === compose(head, map(f));

// filter :: (a -> Bool) -> [a] -> [a]
compose(map(f), filter(compose(p, f))) === compose(filter(p), map(f));
```

이 정리들은 어떤 코드도 없이 타입에서 바로 도출됩니다. 첫번째 것은 배열에서 `head`를 얻고 어떤 함수 `f`에 넣은 것과 먼저 `map(f)`를 모든 원소에 적용한 후에 `head`를 얻는것이 우연히도, 그리고 더 빠르다는 것을 의미합니다.
You don't need any code to get these theorems, they follow directly from the types. The first one says that if we get the `head` of our array, then run some function `f` on it, that is equivalent to, and incidentally, much faster than, if we first `map(f)` over every element then take the `head` of the result.

아마 이것이 그저 상식이라고 생각할 수도 있겠지요. 그러나 제가 마지막으로 체크한 바로는 컴퓨터는 상식이 없더군요. 사실 컴퓨터는 이런 종류의 코드 최적화를 자동적으로 할 형식적인 방법을 가지고 있어요. 수학은 직관을 형식화하는 방법을 가지고 있어요. 그리고 이것은 컴퓨터 논리의 견고한 지역 사이에서 도움이 되지요.
You might think, well that's just common sense. But last I checked, computers don't have common sense. Indeed, they must have a formal way to automate these kind of code optimizations. Maths has a way of formalizing the intuitive, which is helpful amidst the rigid terrain of computer logic.

`filter` 정리도 마찬가이입니다. 이것은 만약 `f`와 `p`를 합성해서 필터에 쓴 후 `map`을 통해서 `f`를 적용한 것(`filter`는 원소를 바꾸지 않는 다는 것을 명심하세요. 이것의 타입 명세는 `a`가 변하지 않는다고 말합니다)과 `f`를 매핑한 후 `p`를 이용해 필터링 한것은 언제나 같다고 말하고 있습니다.
The `filter` theorem is similar. It says that if we compose `f` and `p` to check which should be filtered, then actually apply the `f` via `map` (remember `filter` will not transform the elements - its signature enforces that `a` will not be touched), it will always be equivalent to mapping our `f` then filtering the result with the `p` predicate.

이것들은 두개의 예일 뿐이지만 이런 사고 방식을 어떠한 polymorphic type 명세에도 적용할 수 있고 언제나 옳을 거에요. 자바스크립트에서 규칙을 다시 적기를 선언할 수 있게 해 주는 도구들이 있어요. 이것을 `compose` 함수 자체를 통해 직접 할 수도 있어요. 그 과실은 낮게 달려있지만 그 가능성은 끝이 없어요.
These are just two examples, but you can apply this reasoning to any polymorphic type signature and it will always hold. In JavaScript, there are some tools available to declare rewrite rules. One might also do this via the `compose` function itself. The fruit is low hanging and the possibilities are endless.

## 제약

## Constraints

마지막으로 말할 것은 타입을 어떤 인터페이스로 제한할 수 있다는 것입니다.
One last thing to note is that we can constrain types to an interface.

```js
// sort :: Ord a => [a] -> [a]
```

뚱뚱한 화살표 왼쪽에 있는 것은 다음을 의미합니다: `a`는 반드시 `Ord`입니다. 또 다른 말로 `a`는 반드시 `Ord` 인터페이스를 구현해야합니다. `Ord`는 무엇이고 어디서 왔을까요? 타입이 있는 언어에서 이것은 값의 순서를 매길수 있는 정의된 인터페이스를 지칭합니다. 이것은 단지 우리에게 `a`와 `sort` 함수가 무엇을 하는지 뿐 만 아니라 도메인을 제약하기도 합니다. 우리는 이런 인터페이스 선언을 **타입 제약(type constraints)**라고 부릅니다.
What we see on the left side of our fat arrow here is the statement of a fact: `a` must be an `Ord`. Or in other words, `a` must implement the `Ord` interface. What is `Ord` and where did it come from? In a typed language it would be a defined interface that says we can order the values. This not only tells us more about the `a` and what our `sort` function is up to, but also restricts the domain. We call these interface declarations _type constraints_.

```js
// assertEqual :: (Eq a, Show a) => a -> a -> Assertion
```

여기에 두 제약이 있습니다: `Eq`와 `Show`. 이들은 `a`의 동일성을 체크할 수 있고 둘이 다르면 그 차이를 출력할수 있음을 보장합니다.
Here, we have two constraints: `Eq` and `Show`. Those will ensure that we can check equality of our `a`s and print the difference if they are not equal.

우리는 다른 제약들도 보게 될 것이고 이 후의 장에서 좀 더 형태를 갖추게 될 거에요.
We'll see more examples of constraints and the idea should take more shape in later chapters.

## 요약

힌들리-밀너 타입 명세는 함수형 세상에서 일반적입니다. 읽고 쓰기 쉽지만 타입 명세 만으로 프로그램을 이해하는 기술을 마스터하기에는 시간이 걸립니다. 여기서 부터 모든 줄에 타입 명세를 적을 것입니다.
Hindley-Milner type signatures are ubiquitous in the functional world. Though they are simple to read and write, it takes time to master the technique of understanding programs through signatures alone. We will add type signatures to each line of code from here on out.

[08 장: Tupperware](ch08-kr.md)
