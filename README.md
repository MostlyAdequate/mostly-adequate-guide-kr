[![cover](images/cover.png)](SUMMARY.md)

## 책 소개

이 책은 일반적인 함수형 프로그래밍 패러다임에 대한 책이다. 우리는 세계에서 가장 유명한 함수형 언어인 '자바스크립트'를 사용할 것이다. 현재의 자바스크립트 관련 문화가 주로 명령형imperative 패러다임이기 때문에, 이런 선택이 조금은 잘못된 것 아닌가 하고 느낄 지도 모르겠다. 하지만 다음과 같은 이유로, 나는 자바스크립트를 활용하는 것이 FP를 배우는 데 가장 좋은 선택이 될 수 있다고 믿는다.

 * **매일 업무에 자바스크립트를 사용하고 있는 프로그래머가 많다**

    따라서, 소수만 좋아하는 함수형 프로그래밍 언어에서 자잘한 토이 프로젝트에나 적용해 보는 대신, 실전에서 매일매일 자신이 배운 함수형 프로그래밍 지식을 적용하고 연습할 수 있다. 


 * **프로그램을 작성하기 위해 필요한 여러가지 다른 내용을 다시 배울 필요가 없다.**

    순수 함수형 언어에서는 모나드monad의 도움 없이 변수의 로그를 찍어보거나, DOM 노드를 읽어볼 수 없다. 하지만 자바스크립트에서는 코드 기반을 더 깔끔하게 유지하기 위해서 그런 기능을 살짝살짝 사용할 수 있다. 또한, 자바스크립트가 다양한 패러다임을 지원하기 때문에, 여러분이 잘 모르는 부분이 있다면 최후의 수단으로 기존의 방식을 활용해 문제를 활용할 수도 있다.


 * **자바스크립트는 최상급의 함수형 코드를 작성할 수 있는 모든 기능을 완전히 지원한다.**

    소수의 라이브러리만 추가하면 하스켈Haskell이나 스칼라Scala와 같은 언어가 제공하는 기능을 흉내낼 수 있는 모든 특징을 활용할 수 있다. 객체 지향 프로그래밍이 현재 업계를 지배하고 있지만, 자바스크립트의 OOP는 분명 약간 이상하다. 이는 마치 고속도로를 벗어나서 캠핑을 하거나, 구두 위에 고무덧신galoshes을 신고 탭댄스를 추는 것과 같다. `this`가 부지중에 바뀌지 않도록 `bind`를 여기저기서 사용해야 하며, (아직) 클래스class도 없고, `new` 키워드를 빼먹은 경우 생기는 이상한 작동을 막기 위한 여러가지 장치를 만들어야 하고, 클로저closure를 통해서만 전용private 멤버를 정의할 수 있다. 일반적인 프로그래머에게 이보다는 FP(함수형 프로그래밍)가 더 자연스러워 보인다.
   
정적 타입을 제공하는 함수형 언어가 의심할 여지 없이 이 책에서 설명하는 스타일로 코딩하는 가장 좋은 환경일 것이다. 자바스크립트는 단지 함수형 패러다임을 배우는 수단일 뿐이며, 그 패러다임을 적용하는 것은 여러분에게 달려있다. 운 좋게도, 함수형 패러다임에 접근하는 경로는 수학적이며, 따라서 어떤 어디에서든 통할 수 있다. 이 책을 다루고 나면, 수학쪽으로 경도된 환경이라면 스위프트제드swiftz, 스칼라제드scalaz, 하스켈, 퓨어스크립트purescript 등을 막론하고 편안함을 느낄 수 있을 것이다. 

### (영문판) 기트북(더 보기 좋은 환경을 제공함) - 한글판은 나중에 제공

* [온라인에서 읽기](https://drboolean.gitbooks.io/mostly-adequate-guide/content/)
* [EPUB 다운로드](https://www.gitbook.com/download/epub/book/drboolean/mostly-adequate-guide)
* [Mobi(킨들Kindle) 다운로드](https://www.gitbook.com/download/mobi/book/drboolean/mostly-adequate-guide)

### (영문판) 직접 만드는 방법

```
git clone https://github.com/DrBoolean/mostly-adequate-guide.git

cd mostly-adequate-guide/
npm install gitbook-cli -g
gitbook init

brew update
brew install Caskroom/cask/calibre

gitbook mobi . ./functional.mobi
```

한글판을 직접 만드는 방법은 다음과 같다.

```
git clone https://github.com/MostlyAdequate/mostly-adequate-guide-kr.git

cd mostly-adequate-guide-kr/
npm install gitbook-cli -g
gitbook init

brew update
brew install Caskroom/cask/calibre

gitbook mobi . ./functional.mobi
```

# 목차

[SUMMARY.md](SUMMARY.md) 를 보라.

### 기여하기

[CONTRIBUTING.md](CONTRIBUTING.md) 를 보라.

### 번역

[TRANSLATIONS.md](TRANSLATIONS.md) 를 보라.

### FAQ

[FAQ.md](FAQ.md) 를 보라.



# 향후 계획

* **1부** (현재 1-7장)는 기본적인 내용에 대한 안내다. 초고에서 오류를 발견할 때마다 내용을 갱신할 것이다. 도움의 손길을 언제든 환영한다!
* **2부** (현재 8장 이상)는 펑터functor나 모나드monad로부터 순회가능traversable에 이르는 여러 타입 클래스type class에 대해 다룬다. 변환기transformer나 순수한 적용pure application에 대해 다룰 여유가 있기를 바란다.  
* **3부** 는 실용적인 프로그래밍과 학술적인 불합리성 사이의 미세한 차이를 가늠해 볼 것이다. 코모나드comonad, f-대수f-algebra, 프리 모나드free monad, 요네다yoneda 및 다른 카테고리 이론적인 구성요소들을 살펴볼 것이다. 
