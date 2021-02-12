# 02 장: 일급 함수

## 복습 시간

함수가 "일급" 이라는 말은 다른 보통의 것들과 똑같다는 것을 의미해요. 함수를 특별하게 취급하지 않고 다른 자료형처럼 다룰 수 있다는 것을요. 예를 들면 배열에 저장하거나 함수의 파라미터로 넘겨줄 수도 있어요. 그리고 변수에 할당하는 등 다른 모든 것들도 할 수 있어요.

지금부터 볼 것은 아주 기초적인 자바스크립트이지만 깃헙을 조금만 찾아봐도 이런 코드들을 볼 수 있지만 모두 일부러 이 문제를 회피하고 있기 때문에 한 번 다루고 싶군요. 한번 예시를 들어볼까요?

```js
const hi = (name) => `Hi ${name}`;
const greeting = (name) => hi(name);
```

여기서 `hi`를 감싸는 `greeting` 함수는 아무것도 하는 일이 없어요. 왜일까요? 바로 자바스크립트에서 함수는 **호출할 수 있기**때문지요. `hi`의 끝에 `()`가 있을 때 `hi`는 실행되고 값을 반환합니다. 그렇지 않을 때는 그저 변수에 할당된 함수를 반환하지요.

```js
hi; // name => `Hi ${name}`
hi("jonas"); // "Hi jonas"
```

`greeting`은 그저 `hi`를 자신과 같은 인자로 호출하기 때문에 더 단순하게 쓸 수 있어요.

```js
const greeting = hi;
greeting("times"); // "Hi times"
```

다시 말해서, `hi`가 벌써 하나의 인자를 받는 함수인데 왜 굳이 같은 인자로 `hi`를 호출하기만 하는 함수로 감싸나요? 전혀 쓸모가 없군요. 이건 마치 죽을 것 같이 더운 7월에 아주 무거운 파카를 입고 덥다고 불평하는 것 같아요.

이건 함수를 다른 함수로 감싸 단순히 계산을 느리게 하고 코드를 장황하게 하는 안좋은 방법입니다(잠시 후에 이유를 알아보겠지만 유지 보수와도 관련이 있어요).

더 나아가기 전에 이것을 잘 이해하는 것이 중요하기 때문에 npm 패키지에서 찾은 재미있는 예시를 보겠습니다.

```js
// 무식한 방법
const getServerStuff = (callback) => ajaxCall((json) => callback(json));

// 똑똑한 방법
const getServerStuff = ajaxCall;
```

세상은 이것과 똑같은 ajax 코드들로 더렵혀져 있어요. 위의 두 코드가 같은 이유를 설명해 볼게요.

```js
// 이 줄은
ajaxCall((json) => callback(json));

// 이 줄과 같아요.
ajaxCall(callback);

// 따라서 getServerStuff를 개선할 수 있어요.
const getServerStuff = (callback) => ajaxCall(callback);

// 그리고 다시 이 코드와 같고요.
const getServerStuff = ajaxCall; // <-- 엄마 보세요, ()가 없어요.
```

여러분, 이것이 제대로 된 코드입니다. 내가 왜 이렇게 끈질기게 구는지 이해할 수 있도록 한번 더 예시를 들어볼게요.

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

이 말도 안되는 컨트롤러는 99% 하는 것이 없어요. 그리고 다음처럼 고쳐쓸 수 있어요.

```js
const BlogController = {
  index: Views.index,
  show: Views.show,
  create: Db.create,
  update: Db.update,
  destroy: Db.destroy,
};
```

아니면 그저 Views와 Db를 한군데 모아둘 수도 있어요. View와 Db를 그저 묶어두기만 하기 때문이죠.

## 일급 함수의 장점

좋아요, 이제 일급 함수의 장점을 한번 찾아보죠. `getServerStuff`와 `BlogController` 예에서 본 것 처럼 유지보수를 해야할 쓸모없는 코드 양만 늘리면서 아무런 일도 하지 않는 레이어를 늘리기는 간단합니다.

더군다나 그런 필요없이 싸여진 함수를 변경해야 한다면 그 함수의 감싸고 있는 함수도 바꿔야해요.

```js
httpGet("/post/2", (json) => renderPost(json));
```

`httpGet`이 `err`도 줄 수도 있다고 한다면 우리는 돌아가서 "접착제"를 바꿔야해요.

```js
// 어플리케이션의 모든 httpGet 호출로 가서 명시적으로 err 넘겨주기
httpGet("/post/2", (json, err) => renderPost(json, err));
```

만약 일급함수를 이용했다면 바꿔야할 부분은 훨씬 적었을 거에요.

```js
// renderPost는 httpGet 안에서 원하는 만큼의 인자를 받아서 호출됩니다
httpGet("/post/2", renderPost);
```

불필요한 함수를 만드는 것말고 다른 문제도 있어요. 우리는 인자의 변수명을 짓고 변수를 참조해야해요. 당신도 알다시피 이름붙이기는 꽤 문제가 있답니다. 특히 프로젝트가 오래되고 요구사항이 바뀔수록 잘못된 이름이 많아지.

프로젝트에서 같은 개념을 여러가지 이름으로 부르면 혼란 생기기 쉽습니다. 또 일반적인 코드상의 문제도 있어요. 예를 들면 다음 두 함수를 정확히 같은 일을 하지만 하나는 다른 것보다 훨씬 더 일반적이고 재사용할 수 있을 것 같이 느껴집니다.

```js
// 우리 블로그에 특정된다.
const validArticles = articles =>
  articles.filter(article => article !== null && article !== undefined),

// 다른 프로젝트에서도 사용 가능할 것 같다.
const compact = xs => xs.filter(x => x !== null && x !== undefined);
```

특정한 이름을 사용하면 우리는 우리 스스로를 특정한 데이터에 한정시킵니다(이 예제에서는 `articles`). 이런 일은 꽤나 자주 일어나고 똑같은 것을 다시 만들게 하는 일의 이유가 됩니다.

저는 객체 지향 코드에서처럼 당신이 `this`가 당신의 급소를 치러 온다는 것을 알고 있어야 한다고 말하고 싶어요. 만약 `this`를 사용하고 있는 함수를 일급이라고 부른다면 이 잘못된 추상화가 우리에게 화를 낼 것입니다.

```js
const fs = require("fs");

// 무서워요
fs.readFile("freaky_friday.txt", Db.save);

// 덜 무서워요
fs.readFile("freaky_friday.txt", Db.save.bind(Db));
```

함수를 자신에게 bind 해서 `Db`는 그의 쓰레기 같은 원래의 코드에 마음껏 접근할 수 있게 됐어요. 저는 `this`가 더러운 기저귀인 것처럼 피하려고 해요. 함수형 코드를 쓸 때 `this`를 쓸 필요가 하나도 없어요. 반면 다른 라이브러리와 대화할 때는 우리 주변의 잘못된 세상을 묵인해야 할지도 몰라요.

어떤 사람은 `this`가 성능을 최적화하는데 필요하다고 말해요. 그러나 당신이 세세한 것까지 최적화하는 사람이라면 이 책을 덮어주세요. 만약 환불이 안되더라고 좀 더 사소한 것과 교환할 수 있을 거에요.

이제 이것들과 함께 앞으로 나아갈 준비가 되었습니다.

[03 장: 순수 함수와 함께하는 순수한 기쁨](ch03-kr.md)
