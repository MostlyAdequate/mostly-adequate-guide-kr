# Chapter 02: First Class Functions

# 02 장: 일급 함수

## 복습 시간

함수가 "일급"이라고 말 할 때는 그들이 다른 보통의 것과 같다는 것을 말합니다. 함수를 특별한 점 없이 다른 자료형처럼 다룰 수 있어요. 배열에 저장할 수 있고 함수의 파라미터로 넘겨줄 수도 있고 변수에 할당할 수도 있고 다른 것들도 할 수 있어요.

이것은 기초적인 자바스크립트이지만 깃헙에 있는 코드를 조금만 찾아봐도 집단적인 회피 또는 이 사실에 대한 퍼져있는 무시 때문에 한 번 언급할만할 가치가 있어요. 가짜 예를 들어볼까요? 그래야죠.
That is JavaScript 101, but worth mentioning since a quick code search on github will reveal the collective evasion, or perhaps widespread ignorance of this concept. Shall we go for a feigned example? We shall.

```js
const hi = (name) => `Hi ${name}`;
const greeting = (name) => hi(name);
```

여기서 `hi`를 감싸는 `greeting` 함수는 전혀 하는 일이 없어요. 왜일까요? 바로 자바스크립트에서 함수는 **호출할 수 있기**때문지요. `hi`의 끝에 `()`가 있을 때 `hi`는 실행되고 값을 반환합니다. 그렇지 않을 때는 그저 변수에 할당된 함수를 반환하지요. 확실히 하기 위해 스스로 한번 해보세요.
Here, the function wrapper around `hi` in `greeting` is completely redundant. Why? Because functions are _callable_ in JavaScript. When `hi` has the `()` at the end it will run and return a value. When it does not, it simply returns the function stored in the variable. Just to be sure, have a look yourself:

```js
hi; // name => `Hi ${name}`
hi("jonas"); // "Hi jonas"
```

`greeting`은 그저 `hi`를 같은 인자로 호출하기 때문에 단순히 다음처럼 쓸 수 있어요.
Since `greeting` is merely in turn calling `hi` with the very same argument, we could simply write:

```js
const greeting = hi;
greeting("times"); // "Hi times"
```

따른 말로, `hi`는 이미 하나의 인자를 기대하는 함수인데 왜 굳이 단순히 같은 인자로 `hi`를 호출하는 함수로 감싸나요? 전혀 말이 되지 않아요. 이건 마치 찜통인 7월에 가장 무거운 파카를 입고 얼음 과자를 달라고 하는 것 같아요.
In other words, `hi` is already a function that expects one argument, why place another function around it that simply calls `hi` with the same bloody argument? It doesn't make any damn sense. It's like donning your heaviest parka in the dead of July just to blast the air and demand an ice lolly.

이건 얄밉도록 장황하고 나타난 바와 같이 함수를 다른 함수로 감싸 단순히 평가를 지연하는 나쁜 방법입니다(잠시 후에 이유를 알아보겠지만 유지 보수와 관련이 있어요).
It is obnoxiously verbose and, as it happens, bad practice to surround a function with another function merely to delay evaluation (we'll see why in a moment, but it has to do with maintenance)

이것을 잘 이해하는 것은 더 나아가기 전에 중요하기 때문에 npm 패키지에서 발굴된 재미있는 몇가지 예를 보겠습니다.
A solid understanding of this is critical before moving on, so let's examine a few more fun examples excavated from the library of npm packages.

```js
// 무식한 방법
// ignorant
const getServerStuff = (callback) => ajaxCall((json) => callback(json));

// 개몽된 방법
// enlightened
const getServerStuff = ajaxCall;
```

세상은 정확이 이와 같은 ajax 코드들로 더렵혀져 있어요. 여기 이 두개가 같은 이유가 있어요.
The world is littered with ajax code exactly like this. Here is the reason both are equivalent:

```js
// 이 줄은
// this line
ajaxCall((json) => callback(json));

// 이 줄과 같고
// is the same as this line
ajaxCall(callback);

// 따라서 getServerStuff를 리팩터할 수 있어요.
// so refactor getServerStuff
const getServerStuff = (callback) => ajaxCall(callback);

// 그리고 이 코드와 같고요.
// ...which is equivalent to this
const getServerStuff = ajaxCall; // <-- 엄마 보세요, ()가 없어요.
```

그리고 여러분, 이것이 일이 어떻게 이루어지는가 입니다. 내가 왜 이렇게 끈질기게 구는지 이해할 수 있도록 한번 더 할게요
And that, folks, is how it is done. Once more so that we understand why I'm being so persistent.

```js
const BlogController = {
  index(posts) {
    return Views.index(posts);
  },
  show(post) {
    return Views.show(post);
  },
  create(attrs) {
    return Db.create(attrs);
  },
  update(post, attrs) {
    return Db.update(post, attrs);
  },
  destroy(post) {
    return Db.destroy(post);
  },
};
```

이 말도 안되는 콘트롤러는 99% 비어있어요. 다음처럼 고쳐쓸 수 있어요
This ridiculous controller is 99% fluff. We could either rewrite it as:

```js
const BlogController = {
  index: Views.index,
  show: Views.show,
  create: Db.create,
  update: Db.update,
  destroy: Db.destroy,
};
```

... 또는 그저 Views와 Db를 한군데 모아둘 수도 있어요.
... or scrap it altogether since it does nothing more than just bundle our Views and Db together.

## 왜 일급 함수를 선호하는가

## Why Favor First Class?

좋아요, 이제 일급 함수의 장점을 한번 찾아보죠. `getServerStuff`와 `BlogController` 예에서 본 것 처럼 아무 가치 없이 단지 유지보수해야할 쓸모없는 코드 양만 늘리면서 다른 것을 가르키는 레이어를 늘리기가 쉬워요.
Okay, let's get down to the reasons to favor first class functions. As we saw in the `getServerStuff` and `BlogController` examples, it's easy to add layers of indirection that provide no added value and only increase the amount of redundant code to maintain and search through.

또한 그런 쓸대없이 싸인 함수가 변해야한다면 우리는 그 함수의 래퍼 함수도 바꿔야해요.
In addition, if such a needlessly wrapped function must be changed, we must also need to change our wrapper function as well.

```js
httpGet("/post/2", (json) => renderPost(json));
```

`httpGet`이 `err`도 줄 가능성이 있다고 한다면 우리는 뒤로 돌아가서 "접착제"를 바꿀 필요가 있어요.
If `httpGet` were to change to send a possible `err`, we would need to go back and change the "glue".

```js
// 어플리케이션의 모든 httpGet 호출로 가서 명시적으로 err 넘겨주기
// go back to every httpGet call in the application and explicitly pass err along.
httpGet("/post/2", (json, err) => renderPost(json, err));
```

만약 일급함수를 이용했다면 바꿔야할 부분은 훨씬 적었을 거에요.
Had we written it as a first class function, much less would need to change:

```js
// renderPost는 httpGet 안에서 원하는 만큼의 인자를 받아서 호출됩니다
// renderPost is called from within httpGet with however many arguments it wants
httpGet("/post/2", renderPost);
```

불필요한 함수의 제거 외에도 우리는 반드시 인자의 이름을 붙이고 참조해야해요. 당신도 알다시피 이름붙이기는 꽤 문재가 있답니다. 특히 프로젝트가 나이를 먹고 요구사항이 바뀔수록 잘못된 이름이 많아지지요.
Besides the removal of unnecessary functions, we must name and reference arguments. Names are a bit of an issue, you see. We have potential misnomers - especially as the codebase ages and requirements change.

프로젝트 내에서 같은 개념을 여러가지 이름으로 부르는 것은 혼란의 일반적인 원천입니다. 또 일반적인 코드 이슈도 있어요. 예를들면 이 두 함수를 정확히 같은 것을 하지만 하나는 다른 것보다 확실히 저 일반적이고 재사용할 수 있을 것 같이 느껴집니다.
Having multiple names for the same concept is a common source of confusion in projects. There is also the issue of generic code. For instance, these two functions do exactly the same thing, but one feels infinitely more general and reusable:

```js
// 우리 블로그에 특정
// specific to our current blog
const validArticles = articles =>
  articles.filter(article => article !== null && article !== undefined),

// 미래의 프로젝트에서도 사용 가능
// vastly more relevant for future projects
const compact = xs => xs.filter(x => x !== null && x !== undefined);
```

특정한 이름을 사용하므로써 우리는 우리 스스로를 특정한 데이터에 묶습니다(이 예제에서는 `articles`). 이런 일은 꽤 자주 일어나고 같은것을 다시 만들게 하는 것의 원천이 됩니다.
By using specific naming, we've seemingly tied ourselves to specific data (in this case `articles`). This happens quite a bit and is a source of much reinvention.

저는 꼭 이것을 말하고 싶어요: 객체 지향 코드에서처럼 당신은 `this`가 당신의 경정맥을 물으로 온다는 것을요. 만약 함수가 `this`를 사용하고 이 함수를 일급이라고 부르면 우리는 이 새어나가는 추상화의 분노의 대상이 될 거에요.
I must mention that, just like with Object-Oriented code, you must be aware of `this` coming to bite you in the jugular. If an underlying function uses `this` and we call it first class, we are subject to this leaky abstraction's wrath.

```js
const fs = require("fs");

// 무서워요
// scary
fs.readFile("freaky_friday.txt", Db.save);

// 덜 무서워요
// less so
fs.readFile("freaky_friday.txt", Db.save.bind(Db));
```

그것 자신에게 묶임으로써 `Db`는 그것의 원래의 쓰래기 코드에 마음껏 접근할 수 있게 됬어요. 저는 `this`가 더러운 기저귀인것처럼 사용하는 것을 피해요. 함수형 코드를 쓸 때 `this`를 쓸 필요가 하나도 없어요. 반면 다른 라이브러리와 대화할 때는 우리 주변의 미친 세상을 묵인해야 할지도 몰라요.
Having been bound to itself, the `Db` is free to access its prototypical garbage code. I avoid using `this` like a dirty nappy. There's really no need when writing functional code. However, when interfacing with other libraries, you might have to acquiesce to the mad world around us.

어떤 이는 `this`가 최적화하는데 필요하다고 말해요. 만약 당신이 세세한 것 까지 최적화하는 사람이라면 이 책을 덮어주세요. 만약 환불이 안된다면 아마도 이것을 좀 더 사소한 것으로 바꿀 수 있을 거에요.
Some will argue that `this` is necessary for optimizing speed. If you are the micro-optimization sort, please close this book. If you cannot get your money back, perhaps you can exchange it for something more fiddly.

이제 이것들을 유념해두고 앞으로 나아갈 준비가 되었습니다.
And with that, we're ready to move on.

[03 장: 순수 함수와 함께하는 순수한 기쁨](ch03-kr.md)
[03 장: Pure Happiness with Pure Functions](ch03-kr.md)
