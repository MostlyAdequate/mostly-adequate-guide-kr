# 01 장: 우리는 무얼 하고 있나?

## 소개

안녕하세요, 저는 Franklin Frisby 교수입니다. 만나서 반갑습니다. 제가 당신에게 함수형 프로그래밍에대해 조금 알려드리는 동안 우리는 조금 어울리게 되겠지요. 저에 대해서는 충분히 이야기 했습니다. 당신은 어떤 사람입니까? 저는 당신이 최소한 자바스크립트에 대해 친숙하고 객체지향 프로그래밍 경혐이 조금 있기를 바랍니다. 그리고 당신 자신을 프로그래머라고 생각하고 있기를요. 곤총학 박사가 아니어도 되지만 곤충(bug)를 찾고 잡는 법을 알고 있어야 합니다.

저는 당신이 함수형 프로그래밍에 대한 지식을 가지고 있다고 가정하지 않겠습니다. 그렇게 생각하면 어떤 일이 벌어질지 우리 모두 알고 있잖아요. 하지만 변하는 상태(mutable state)와 제한되지 않은 부수효과(side effects), 규칙 없는 디자인이 만드는 불쾌한 상황을 겪어봤길 기대합니다. 이제 서로 충분히 알게 된 것 같군요. 이제 시작합니다.

이 장의 목적은 함수형 프로그램을 쓰고난 후에 우리가 무엇이 되어있을지 감을 잡게 해 주는 것입니다. 다음 장들을 이해하기 위해서는 무엇이 _함수형_ 프로그램을 만드는지에 대해 어느정도 알고있어야합니다. 그렇지 않으면 목적을 잃고
The purpose of this chapter is to give you a feel for what we're after when we write functional programs. In order to be able to understand the following chapters, we must have some idea about what makes a program _functional_. Otherwise we'll find ourselves scribbling aimlessly, avoiding objects at all costs - a clumsy endeavor indeed. We need a clear bullseye to hurl our code at, some celestial compass for when the waters get rough.

Now, there are some general programming principles - various acronymic credos that guide us through the dark tunnels of any application: DRY (don't repeat yourself), YAGNI (ya ain't gonna need it), loose coupling high cohesion, the principle of least surprise, single responsibility, and so on.

I won't belabor you by listing each and every guideline I've heard throughout the years... The point of the matter is that they hold up in a functional setting, although they're merely tangential to our ultimate goal. What I'd like you to get a feel for now, before we get any further, is our intention when we poke and prod at the keyboard; our functional Xanadu.

<!--BREAK-->

## 짧은 만남 A Brief Encounter

약간 이상한 코드로 시작합시다. 여기 갈매기 어플리케이션이 있습니다. 무리들이 결합(cojoin)하면 더 큰 무리가 되고 새끼를 치면 다른 무리의 수만큼 곱해집니다. 이것은 좋은 객체지향 코드가 아니지만 우리의 할당에 기반한 현대적인 접근 방식의 위험을 강조하기 위한 코드입니다. 한번 보세요:
Let's start with a touch of insanity. Here is a seagull application. When flocks conjoin they become a larger flock, and when they breed, they increase by the number of seagulls with whom they're breeding. Now, this is not intended to be good Object-Oriented code, mind you, it is here to highlight the perils of our modern, assignment based approach. Behold:

```js
class Flock {
  constructor(n) {
    this.seagulls = n;
  }

  conjoin(other) {
    this.seagulls += other.seagulls;
    return this;
  }

  breed(other) {
    this.seagulls = this.seagulls * other.seagulls;
    return this;
  }
}

const flockA = new Flock(4);
const flockB = new Flock(2);
const flockC = new Flock(0);
const result = flockA
  .conjoin(flockC)
  .breed(flockB)
  .conjoin(flockA.breed(flockB)).seagulls;
// 32
```

세상의 어떤 사람이 이런 혐오스러운 것을 만들겠어요? 내부 상태 변화를 추적하기가 너무 힘들군요. 그리고 세상에, 심지어 답도 틀렸어요! `16`이 되어야하지만 `flockA`가 영구적으로 바뀌었습니다. 불쌍한 `flockA`. 이것이 I.T.의 난세입니다! 이것이 야생의 산수입니다!
Who on earth would craft such a ghastly abomination? It is unreasonably difficult to keep track of the mutating internal state. And, good heavens, the answer is even incorrect! It should have been `16`, but `flockA` wound up permanently altered in the process. Poor `flockA`. This is anarchy in the I.T.! This is wild animal arithmetic!

이 프로그램을 이해하지 못해도 괜찮습니다. 저도 모르겠거든요. 기억해야할 점은 이런 작은 예제에서조차 상태와 변하는 값(mutable values)은 쫒아가기 힘들다는 것입니다.
If you don't understand this program, it's okay, neither do I. The point to remember here is that state and mutable values are hard to follow, even in such a small example.

이번에는 함수형 접근방법을 사용해서 한번 더 시도해봅시다.
Let's try again, this time using a more functional approach:

```js
const conjoin = (flockX, flockY) => flockX + flockY;
const breed = (flockX, flockY) => flockX * flockY;

const flockA = 4;
const flockB = 2;
const flockC = 0;
const result = conjoin(
  breed(flockB, conjoin(flockA, flockC)),
  breed(flockA, flockB)
);
// 16
```

와, 이번에는 올바른 답을 얻었네요. 좀 더 적은 코드로요. 함수 중첩은 약간 헷갈리네요... (5장에서 이 문제를 해결할 거예요). 좀 낫지만 조금 더 깊게 가볼까요. 스페이드를 스페이드라고 부르면 장점이 있습니다. 그렇게 했다면 우리는 그저 덧셈(`cojoin`)과 곱셈(`breed`)를 하고 있다는 것을 알았을 거에요.
Well, this time we got the right answer. With much less code. The function nesting is a tad confusing... (we'll remedy this situation in ch5). It's better, but let's dig a little bit deeper. There are benefits to calling a spade a spade. Had we scrutinized our custom functions more closely, we would have discovered that we're just working with simple addition (`conjoin`) and multiplication (`breed`).

이 두 함수에는 이름 말고 다른 특별한 점은 하나도 없어요. 우리의 함수들을 `multiply`와 `add`라고 다시 이름지어서 정체성을 드러내봅시다.
There's really nothing special at all about these two functions other than their names. Let's rename our custom functions to `multiply` and `add` in order to reveal their true identities.

```js
const add = (x, y) => x + y;
const multiply = (x, y) => x * y;

const flockA = 4;
const flockB = 2;
const flockC = 0;
const result = add(
  multiply(flockB, add(flockA, flockC)),
  multiply(flockA, flockB)
);
// 16
```

그리고 이제 우리는 고대의 지식을 얻게 되었어요.
And with that, we gain the knowledge of the ancients:

```js
// 결합법칙
// associative
add(add(x, y), z) === add(x, add(y, z));

// 교환법칙
// commutative
add(x, y) === add(y, x);

// 항등원
// identity
add(x, 0) === x;

// 분배법칙
// distributive
multiply(x, add(y, z)) === add(multiply(x, y), multiply(x, z));
```

물론 오래되고 믿음직한 수학적 성질들은 편리할 것입니다. 그러나 지금 당장 이해하지 못해도 걱정하지 마세요. 우리들 대부분은 이 산수 법직들을 배운지 오래되었으니까요. 이 법칙들을 이용해 우리의 작은 갈매기 프로그램을 간단하게 만들 수 있는지 한 번 볼까요?
Ah yes, those old faithful mathematical properties should come in handy. Don't worry if you didn't know them right off the top of your head. For a lot of us, it's been a while since we learned about these laws of arithmetic. Let's see if we can use these properties to simplify our little seagull program.

```js
// 기존 코드
// Original line
add(multiply(flockB, add(flockA, flockC)), multiply(flockA, flockB));

// 필요없는 덧셈을 줄이기 위해 항등원 규칙 적용하
// Apply the identity property to remove the extra add
// (add(flockA, flockC) == flockA)
add(multiply(flockB, flockA), multiply(flockA, flockB));

// 분배법칙 적용하기
// Apply distributive property to achieve our result
multiply(flockB, add(flockA, flockA));
```

와!! 우리는 함수 호출하는 것 이외의 코드를 쓸 필요가 없었군요. 지금은 완전성을 위해 `add`와 `multiply`의 정의를 적었지만 굳이 사용할 필요는 없습니다.`add`와 `multiply`를 제공하는 라이브러리가 있을테니까요.
Brilliant! We didn't have to write a lick of custom code other than our calling function. We include `add` and `multiply` definitions here for completeness, but there is really no need to write them - we surely have an `add` and `multiply` provided by some existing library.

아마 당신은 "이런 수학적인 예제는 너무 허수하비의 오류가 아닌가요" 내지는 "실제 프로그램은 이렇게 단순하지 않고 이런 식으로 생각할 수 없어요"라고 생각할지도 몰라요. 저는 우리 대부분은 이미 덧셈과 곱셈에 대해 알고 있고 수학이 얼마나 유용한지 보여주기 쉽기 때문에 예를 선택있어요.
You may be thinking "how very strawman of you to put such a mathy example up front". Or "real programs are not this simple and cannot be reasoned about in such a way." I've chosen this example because most of us already know about addition and multiplication, so it's easy to see how math is very useful for us here.

실망하지 마세요. 이 책에 걸쳐 우리는 약간의 범주론(category theory)과 집합론과 람다 대수(lambda calculus)을 탐험할 것이고 갈매기 예시와 같은 우아하게 간단한 결과를 성취하는 실제 세상의 문제를 적을 거예요. 당신은 수학자일 필요는 없어요. 당신이 "보통의" 프레임워크나 API를 사용하는 것처럼 자연스럽고 쉽게 느껴질 것이에요.
Don't despair - throughout this book, we'll sprinkle in some category theory, set theory, and lambda calculus and write real world examples that achieve the same elegant simplicity and results as our flock of seagulls example. You needn't be a mathematician either. It will feel natural and easy, just like you were using a "normal" framework or API.

아마 모든 어플리케이션을 위와 같은 함수형 코드로 짤 수 있다는 것을 알면 당신은 놀라겠지요. 건전한 성질을 가지는 프로그램을요. 그리고 적은 코드를 쓰지만 이해하기 쉬운 프로그램을요. 매번 바퀴를 발명하지 않는 프로그램을요. 당신이 범죄자라면 무법지대는 좋겠지만 이 책에서는 수학의 법칙을 인정하고 따르기를 원하게 될 거예요.
It may come as a surprise to hear that we can write full, everyday applications along the lines of the functional analog above. Programs that have sound properties. Programs that are terse, yet easy to reason about. Programs that don't reinvent the wheel at every turn. Lawlessness is good if you're a criminal, but in this book, we'll want to acknowledge and obey the laws of math.

우리는 모든 조각이 서로 딱 들어맞는 이론을 원할것이에요. 우리는 우리의 특정한 문제를 일반적이고 조합 가능한 조각으로 표현하고 우리 자신의 문제를 위해 이용하기를 원할 것이에요. 명령형 프로그램의 "모든지 가능한" 접근방식 보다는 좀 더 규율있는 접근방식을 택할 것이에요(나중에 "명령형"에 대한 정확한 정의를 다루겠지만 지금은 함수형 프로그래밍 이외의 모든 것을 의미한다고 생각해주세요). 원리가 있는 수학적 프레임워크 안에서 일하는 것을 당신을 진실로 놀라게 할 것이에요.
We'll want to use a theory where every piece tends to fit together so politely. We'll want to represent our specific problem in terms of generic, composable bits and then exploit their properties for our own selfish benefit. It will take a bit more discipline than the "anything goes" approach of imperative programming (we'll go over the precise definition of "imperative" later in the book, but for now consider it anything other than functional programming). The payoff of working within a principled, mathematical framework will truly astound you.

우리는 함수형 북극성의 반짝임을 봤지만 우리의 여정을 진짜로 시작하기 전에 알아야하는 구체적인 개념이 몇가지 있습니다.
We've seen a flicker of our functional northern star, but there are a few concrete concepts to grasp before we can really begin our journey.

[02 장: 일급 함수](ch02-kr.md)
