---
layout: post
title:  "React.js를 하기 전에 알아야 할 정보들"
date:   2016-07-02 
---

react.js를 공부해보려 사전정보 입수를 위해 구글을 이용해 정보를 찾아서 정리해봅니다.

***

###[React.js][reactjs]

facebook에서 주도하고 있는 오픈소스 프로젝트로, facebook과 instagram에 사용되고 있습니다.

react의 가장 큰 특징은 MVC 페턴에서 V(View) 즉, 화면을 표현하는 부분만 구현하는 라이브러리 입니다.

MVC 패턴 중 View의 역활만 하기 때문에 Model과 Controller의 역활을 하는 부분을 따로 구현할 수 있습니다.

#####참고문헌 

* [react.js 시작하기](http://webframeworks.kr/tutorials/react/getting-started/)
* [블로터:페이스북의 인기 오픈소스 기술, ‘리액트’란?](http://www.bloter.net/archives/233564) 

***

###[browserify][browserify]와 [webpack][webpack]

React.js는 만들어 진지 얼마 안된 라이브러리입니다. 그래서 최신기술을 많이 적용 되어있는데 

ES6(ES2015)를 제대로 사용하기 위해 Module builder가 필요하고

Module builder중 가장 많이 쓰이는 것이 Webpack과 browserify 입니다.

Module builder를 소개하기 전에 Module에 대해서 설명하자면

Module은 어떤 하나의 특화된 기능을 하주 잘 수행하는 어떤 독립된 집합체 이며

대표적으로 jQuery를 들수 있습니다. 

검색을 어느정도 해본결과 react.js는 주로 webpack을 사용하는 경우가 많았으니 참고하시기 바랍니다.


#####참고문헌

 * [browserify와 webpack](http://blog.coderifleman.com/post/112564054684/browserify와-webpack)
 * [Modules, 앞으로 JavaScript 라이브러리가 나아갈 길](http://appletree.or.kr/blog/web-development/javascript/modules-앞으로-javascript-라이브러리가-나아갈-길/)
 * [JavaScript 모듈화 도구, webpack](http://d2.naver.com/helloworld/0239818)

***

###[ES2015(ES6)][ES6]

JavaScript는 ECMAScript(ECMA262)라는 사양을 기반으로 구현되어있습니다. 

현재 모던한 Web 브라우저는 ECMAScript 5.1th Edition을 기반으로 한 JavaScript실행 엔진을 탑재하고 있습니다. 

그리고 다음 버전인 ECMAScript 6th Edition이 현재 재정중으로, 약칭으로 ES6이라는 명칭이 사용되고 있습니다.

ES2015라고 불리우는 이유는 2015년에 만들어졌기 때문입니다.

ES6의 경우 ES5를 사용하면서 불편했던 부분이 많이 해소 되었으며, 최근 모던한 브라우저들에서 지원을 하고 있습니다.

#####참고문헌

 * [ES6시대의 JavaScript](https://gist.github.com/marocchino/841e2ff62f59f420f9d9)
 * [ECMAScript 6 Features](http://seokjun.kr/ecmascript-6-features/)

***

###[Babal][babal]

Node.js는 어느 정도 ES2015의 지원하고 있지만 웹 브라우저 같은 경우는 ES2015를 모두 지원하는 것은 

아니므로 ES2015로 코드를 작성하면 코드를 트랜스파일 해서 ES5 코드로 바꾸어야 실제로 사용할 수 있다. 

JavaScript에서는 CoffeeScript처럼 비슷한 부류의 언어를 JavaScript로 바꾸는 것을 트랜스파일이라고 

부르고 있고 ES2015의 트랜스파일러(transpiler)로는 구글이 만든 Traceur와 [Babel][babal]이 있다.

#####참고문헌

 * [Babel로 ECMAScript 2015 사용하기](https://blog.outsider.ne.kr/1176)
 * [ECMAScript 6를 위한 Babel 기본 사용법](http://haruair.com/blog/2917)
 * [Learn ES2015 with babal](http://babeljs.io/docs/learn-es2015/)

***

###[Meteor.js][Meteorjs]

Meteor는 모던 웹과 모던 어플리케이션을 위한 Full-stack 자바스크립트 플랫폼이라고 정의 되어있습니다.

Meteor는 javascript 단 하나의 언어를 이용하여 모든 환경(어플리케이션 서버, 웹 서버, 모바일 기기)을 

개발할 수 있다는 점이 가장 큰 장점입니다.



#####참고문헌

 * [Meteor.js 시작하기](http://webframeworks.kr/getstarted/meteorjs/)
 * [Meteor 배우는 방법](http://mobicon.tistory.com/466)

***

###[flux][flux]

Flux는 Facebook에서 클라이언트-사이드 웹 어플리케이션을 만들기 위해 사용하는 어플리케이션 아키텍쳐입니다. 

단방향 데이터 흐름을 활용해 뷰 컴포넌트를 구성하는 React를 보완하는 역할을 합니다. 

![flux 구조 설명](http://haruair.github.io/flux/img/flux-simple-f8-diagram-explained-1300w.png)

Flux는 MVC와 다르게 단방향으로 데이터가 흐릅니다. React view에서 사용자가 상호작용을 할 때, 

그 view는 중앙의 dispatcher를 통해 action을 전파하게 됩니다. 

어플리케이션의 데이터와 비지니스 로직을 가지고 있는 store는 action이 전파되면 

이 action에 영향이 있는 모든 view를 갱신한다. 이 방식은 특히 React의 선언형 프로그래밍 스타일 

즉, view가 어떤 방식으로 갱신해야 되는지 일일이 작성하지 않고서도 데이터를 변경할 수 있는 형태에서 편리합니다.


#####참고문헌

 * [flux Overview](http://haruair.github.io/flux/docs/overview.html)


[reactjs]: https://facebook.github.io/react/
[browserify]:http://browserify.org/
[webpack]:https://webpack.github.io/
[flux]:http://facebook.github.io/flux/
[ES6]:http://www.ecma-international.org/ecma-262/6.0/
[babal]:https://babeljs.io
[Meteorjs]:https://www.meteor.com