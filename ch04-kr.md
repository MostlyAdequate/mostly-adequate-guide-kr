# 04 장: 커링

## Can't Live If Livin' Is without You

제 아버지는 사람이 그것들을 얻기 전까지 없이 살 수 있는 것들이 있다고 말씀하신 적이 있어요. 전자레인지나 스마트폰 등등이 그렇지요. 우리 중의 오래된 사람들은 인터넷 없는 삶을 사는 것을 기억할 것이에요. 저에게는 커링이 이 목록에 있습니다.

커링의 개념은 간단해요. 함수가 예상하는 인자보다 적은 인자로 함수를 부르는 것이지요. 그리고 이 때 함수는 나머지 인자를 함수를 받는 함수를 반환해요.

당신은 모든 인자를 한번에 넘겨주거나 단순히 인자를 하나씩 넘겨주는 것 중에 선택할 수 있어요.

```js
const add = (x) => (y) => x + y;
const increment = add(1);
const addTen = add(10);

increment(2); // 3
addTen(2); // 12
```

우리는 인자를 하나 받고 함수를 반환하는 `add`를 만들었어요. 이것을 호출해서 반환되는 함수는 클로져를 이용해 첫번째 인자를 기억하지요. 하지만 두 인자를 한번에 부르는 것은 좀 복잡한 일입니다. 그래서 우리는 함수를 조금 더 쉽게 만들고 호출할 수 있게 해주는 특별한 도우미 함수인 `curry`를 사용해 볼 것입니다.

재미를 위해 몇가지 커리된 함수를 만들어볼까요. 이제부터 우리는 [Appendix A - Essential Function Support](./appendix_a.md)에 정의된 `curry`를 소환할것입니다.

```js
const match = curry((what, s) => s.match(what));
const replace = curry((what, replacement, s) => s.replace(what, replacement));
const filter = curry((f, xs) => xs.filter(f));
const map = curry((f, xs) => xs.map(f));
```

여기서 사용한 패턴은 간단하지만 주목할 점이 있습니다. 저는 의도적으로 데이터(String, Array)를 마지막 인자에 두었어요. 왜 이렇게 했는지는 차차 명확해질겁니다.

(`/r/g` 문법은 **모든 'r'**을 가르키는 정규 표현식입니다. 궁금하다면 [정규 표현식](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)에 대한 글을 읽어보세요.)

```js
match(/r/g, "hello world"); // [ 'r' ]

const hasLetterR = match(/r/g); // x => x.match(/r/g)
hasLetterR("hello world"); // [ 'r' ]
hasLetterR("just j and s and t etc"); // null

filter(hasLetterR, ["rock and roll", "smooth jazz"]); // ['rock and roll']

const removeStringsWithoutRs = filter(hasLetterR); // xs => xs.filter(x => x.match(/r/g))
removeStringsWithoutRs(["rock and roll", "smooth jazz", "drum circle"]); // ['rock and roll', 'drum circle']

const noVowels = replace(/[aeiou]/gi); // (r,x) => x.replace(/[aeiou]/ig, r)
const censored = noVowels("*"); // x => x.replace(/[aeiou]/ig, '*')
censored("Chocolate Rain"); // 'Ch*c*l*t* R**n'
```

여기서 하나나 두개의 인자를 함수에 "미리" 설정하고 함수는 이 인자들을 기억하는 것을 볼 수 있어요.

저는 당신이 Mostly Adequate repository (`git clone https://github.com/MostlyAdequate/mostly-adequate-guide.git`)를 클론하고 위의 코드를 복사한 후에 REPL에서 돌려보는 것을 추천해요. 커리를 해주는 함수는 부록에 정의된 다른 것들과 마찬가지로 `support/index.js` 모듈에 있습니다.

아니면 `npm`에 배포된 버전을 한번 보세요.

```
npm install @mostly-adequate/support
```

## 말장난 조금 더 / 특별한 소스

커링은 여러 방면으로 유용합니다. 우리는 `hasLetterR`과 `removeStringsWithoutRs`, `censored`의 예에서 기본 함수에 몇가지 인자를 줌으로써 새로운 함수를 만드는 것을 봤어요.

또한 하나의 인자에만 작용하는 함수가 배열에 작용할 수 있도록 `map`으로 감싸는 것도 보았지요.

```js
const getChildren = (x) => x.childNodes;
const allTheChildren = map(getChildren);
```

보통 함수가 예상하는 것보다 덜 인자를 넘겨주는 것을 **부분 적용(partial application)**이라고 부릅니다. 함수에 부분 적용을 하면 보일러 플레이트 코드를 많이 줄여줍니다. losash의 커리되지 않은 `map`을 위의 `allTheChildren`에 사용하는 것을 생각해봅시다. 인자의 순서가 다르다는 것을 눈여겨보세요.

```js
const allTheChildren = (elements) => map(elements, getChildren);
```

우리는 그냥 인라인에서 `map(getChildren)`으로 호출할 수 있기 때문에 보통 배열에 적용되는 함수를 따로 정의하지는 않습니다. `sort`, `filter`와 다른 고차 함수들도 마찬가지입니다(**고차 함수**는 함수를 받거나 반환하는 함수입니다.).

**순수 함수**에 대해서 말할 때 그들이 1개의 입력과 1개의 출력을 가지고 있다고 말했지요. 바로 커링이 이것을 합니다. 하나의 인자를 받을 때마다 나머지 인자를 기대하는 함수를 반환합니다. 이것이 오래된 스포츠는 1대 1의 입력입니다.

출력이 다른 함수이지만 이것은 순수합니다. 우리는 한번에 한개 이상의 인자를 줄 수도 있지만 이것은 단순히 편의를 위해 여분의 `()`를 제거하는 것으로 볼 수 있어요.

## 요약

커링은 편리하고 저는 매일 커리된 함수와 함께하는 것을 즐기고 있어요. 커링은 함수형 프로그래밍을 좀 덜 장황하고 지루하게 만들어주는 도구입니다.

우리는 인자를 조금만 넘겨주면서 즉석에서 유용하고 새로운 함수를 만들 수 있어요. 또 함수가 여러 인자를 요구함에도 수학적인 함수 정의를 얻을 수 있어요.

이제 또 다른 도구인 함성(`compose`)를 만나볼까요?

[Chapter 05: Coding by Composing](ch05.md)

## 연습문제

#### 연습문제에 대하여

Throughout the book, you might encounter an 'Exercises' section like this one. Exercises can be
done directly in-browser provided you're reading from [gitbook](https://mostly-adequate.gitbooks.io/mostly-adequate-guide) (recommended).

Note that, for all exercises of the book, you always have a handful of helper functions
available in the global scope. Hence, anything that is defined in [Appendix A](./appendix_a.md),
[Appendix B](./appendix_b.md) and [Appendix C](./appendix_c.md) is available for you! And, as
if it wasn't enough, some exercises will also define functions specific to the problem
they present; as a matter of fact, consider them available as well.

> Hint: you can submit your solution by doing `Ctrl + Enter` in the embedded editor!

#### Running Exercises on Your Machine (optional)

Should you prefer to do exercises directly in files using your own editor:

- clone the repository (`git clone git@github.com:MostlyAdequate/mostly-adequate-guide.git`)
- go in the _exercises_ section (`cd mostly-adequate-guide/exercises`)
- install the necessary plumbing using [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) (`npm install`)
- complete answers by modifying the files named \*exercises\_\*\* in the corresponding chapter's folder
- run the correction with npm (e.g. `npm run ch04`)

Unit tests will run against your answers and provide hints in case of mistake. By the by, the
answers to the exercises are available in files named \*answers\_\*\*.

#### Let's Practice!

{% exercise %}  
Refactor to remove all arguments by partially applying the function.

{% initial src="./exercises/ch04/exercise_a.js#L3;" %}

```js
const words = (str) => split(" ", str);
```

{% solution src="./exercises/ch04/solution_a.js" %}  
{% validation src="./exercises/ch04/validation_a.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}

---

{% exercise %}  
Refactor to remove all arguments by partially applying the functions.

{% initial src="./exercises/ch04/exercise_b.js#L3;" %}

```js
const filterQs = (xs) => filter((x) => match(/q/i, x), xs);
```

{% solution src="./exercises/ch04/solution_b.js" %}  
{% validation src="./exercises/ch04/validation_b.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}

---

Considering the following function:

```js
const keepHighest = (x, y) => (x >= y ? x : y);
```

{% exercise %}  
Refactor `max` to not reference any arguments using the helper function `keepHighest`.

{% initial src="./exercises/ch04/exercise_c.js#L7;" %}

```js
const max = (xs) => reduce((acc, x) => (x >= acc ? x : acc), -Infinity, xs);
```

{% solution src="./exercises/ch04/solution_c.js" %}  
{% validation src="./exercises/ch04/validation_c.js" %}  
{% context src="./exercises/support.js" %}  
{% endexercise %}
