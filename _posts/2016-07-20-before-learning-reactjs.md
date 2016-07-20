---
layout: post
title:  "React.js를 하기 전에 알아야 할 정보들"
date:   2016-07-02 
categories: react reactjs meteor webpack commonjs
---

최근에도 핫했고 지금도 핫하고 앞으로도 핫했으면(내가 사용할꺼니까요 ㅠㅠ)하는 

react.js를 공부해보려 사전정보 입수를 위해 구글을 이용해 정보를 찾아서 정리해봅니다.

***

###[React.js][reactjs offical]

facebook에서 주도하고 있는 오픈소스 프로젝트로, facebook과 instagram에 사용되고 있습니다.

react의 가장 큰 특징은 MVC 페턴에서 V(View)만 즉, 화면을 표현하는 부분만 구현하는 라이브러리 입니다.

MVC 패턴 중 View의 역활만 하기 때문에 Model과 Controller의 역활을 하는 부분을 따로 구현할 수 있습니다.

#####참고문헌 

* [react.js 시작하기][reactjs data]
* [블로터:페이스북의 인기 오픈소스 기술, ‘리액트’란?][reactjs blother] 

***

###[browserify][browserify]와 [webpack][webpack]

React.js는 만들어 진지 얼마 안된 라이브러리입니다. 그래서 최신기술을 많이 적용 되어있는데 

ES6(ES2015)를 제대로 사용하기 위해 Module builder가 필요하고

Module builder중 가장 많이 쓰이는 것이 Webpack과 browserify 입니다.

Module builder를 소개하기 전에 Module에 대해서 설명하자면

Module은 어떤 하나의 특화된 기능을 하주 잘 수행하는 어떤 독립된 집합체 이며

대표적으로 jQuery를 들수 있습니다. 

검색을 어느정도 해본결과 react.js는 주로 webpack을 사용하는 경우가 많았으니 

참고하시기 바랍니다.


#####참고문헌

 * [browserify와 webpack](http://blog.coderifleman.com/post/112564054684/browserify와-webpack)
 * [Modules, 앞으로 JavaScript 라이브러리가 나아갈 길](http://appletree.or.kr/blog/web-development/javascript/modules-앞으로-javascript-라이브러리가-나아갈-길/)

***

###[flux][flux offical]




[reactjs offical]: https://facebook.github.io/react/
[reactjs blother]: http://www.bloter.net/archives/233564
[reactjs data]: http://webframeworks.kr/tutorials/react/getting-started/

[browserify]:http://browserify.org/
[webapck]:https://webpack.github.io/

[flux offical]: http://facebook.github.io/flux/
