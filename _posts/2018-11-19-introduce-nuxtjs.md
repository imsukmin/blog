---
layout: post
title: "[번역] 'Universal application code structure in Nuxt.js' translated by korean"
date: "2018-11-19"
---

# Nuxt.js의 유니버셜 어플리케이션 코드 구조

[See original article (원문 보기)](https://medium.freecodecamp.org/universal-application-code-structure-in-nuxt-js-4cd014cc0baa)

> **유니버셜 어플리케이션의 정확하고 확실한 한글말(번역)을 찾을 수 도 없고 생각도 나지 않아 그냥 한글로 써놨습니다. (추측에는 NUXT의 소개말에서 온 어원으로 추측됩니다.)**

> 한글로 적어놨으나 의미전달이 불분명하다고 생각되는부분에는 소괄호로 영어나 한글을 첨언하였습니다.

> 오역에 대한 오류사항 수정관련 피드백 언제든지 환영합니다.

## Nuxt.js 코드 구조 짧은 요약입니다

아직 nuxt.js 프레임 워크가 새롭고, nuxt.js의 많은 폴더가 생성된것에 놀라(overwhelmed)셨습니까? 또한 대부분의 폴더들이 비어있거나 readme.md 파일만 생성되어 있는 것에 놀랐(surprised)나요? 그때는 내가 1년 전 뭘 몰랐을 때 입니다. 그 이후로 나는 각 폴더가 Nuxt 프로젝트에서 동작하는 마법(적인 자동화)을 배우고, 문서화하는 것을 항상 원했습니다.

그리고 지금, 몇몇 프로젝트에 이 프레임워크(nuxt)를 통해 구현해 본 후, 내가 이해한 서버 렌더링 된 Vue 어플리케이션을 각각의 폴더가 어떻게 작동 하는지에 대한 것을 문서화 했습니다.

![nuxtjs code structure diagram](https://cdn-images-1.medium.com/max/2000/1*lwIEF0F3eDlMKR1hKZic7Q.jpeg)

위의 다이어그램은 [Vue SSR 가이드](https://ssr.vuejs.org/guide/structure.html#introducing-a-build-step)를 기반으로하며 Nuxt.js를 연관하여 확장되었습니다. 한 눈에 보기에도, _당신의 유니버설 어플리케이션 코드_ 섹션에서 폴더구조가 다른지와, 코드가 Nuxt에 의해 패키지화 되고 Webpack에 번들되는 방법을 볼 수 있습니다

이 글은 Nuxt SSR에 대한 튜토리얼도 아니고 완벽한 안내서도 아닙니다. 오히려 유니버셜 어플리케이션의 시작에 필요한 내용(part)을 보여줍니다.

다이어그램의 맨 위에 모듈, serverMiddleware 및 플러그인이 있지만 먼저 Store부터 시작합시다.

### Vuex 저장소 (/store)

Vuex는 nuxt.js에 이미 포함되어있지만, _/store_ 디렉토리에 Vuex 저장소를 만들고 저장소를 만들지 않는 한 활성화되지 않습니다.

이것은 모든 데이터 기반 프로젝트를 위한 매우 특별한 디렉토리입니다. 여기에서 데이터 저장소를 만들고 _nuxtServerInit_ 작업을 정의 할 수 있습니다. 이것은 거의 첫 번째 라이프 사이클 훅에서 발생합니다!

```javascript
const createStore = () => {  
  return new Vuex.Store({  
      ...
  })  
}
```

사용자가 어플리케이션에 처음 액세스 할 때, 이것은 (Data)저장소를 채우거나, 업데이트하는 데 도움이됩니다. 또한 응용 프로그램 전체에서 데이터 상태를 유지 관리합니다.

### Route Middleware (/middleware)

Nuxt에는 세 가지 종류의 라우트 미들웨어가 있습니다.
그것들은 _/middleware_ 디렉토리 하나의 모두 정의됩니다.

여기서 다음과 같은 방법으로 사용할 수 있습니다:

- Global middleware — (Nuxt config에 추가하고 and 모든 routes 적용됩니다.)

```javascript
// nuxt.config.js

export default {  
  router: {  
    middleware: 'authenticated'  
  },  
}
```

- Layout middleware (layouts 폴더 안 파일에 추가하고, 일치하는 라우트 그룹에 영향을 줍니다, 즉 인증 된 사용자 만 보거나 액세스 할 수 있는 특정 페이지 같은 것을 예로 들수 있습니다.)

```javascript
// layouts/default.vue

export default {  
  middleware: 'authenticated-basic-plan-user'
}
```

- Page middleware (page component 폴더 안 파일에 추가하고 각각의 라우트(=경로)에서 작동합니다.)

```javascript
// pages/index.vue

export default {
  middleware: 'subscribed'
}
```

위의 미들웨어는 정확히 위의 순서로 처리됩니다. 즉, 우선 순위를 변경 할 수 없습니다. 따라서 신중하게 검토하고 신중하게 계획을 세워야만, 그것들(middleware)을 최대한 활용할 수 있습니다.

### Vue 컴퍼넌트

Nuxt 프로젝트에서 _.vue_ 파일이 생성되는 세 개의 디렉토리가 있습니다.

#### **1. Page components 👑 (/pages)**

이것은 어플리케이션의 뷰와 라우트와 관련되어 있는 모든 디렉터리 중 가장 중요한 디렉터리입니다. 여기에 생성 된 Vue.js 구성 요소는 어플리케이션 route로 직접 변환됩니다.

페이지 컴퍼넌트의 진정한 강점은 동적 경로에 있습니다. 당신은 이것(동적 경로)을 사용하여 SEO 친화적인 데이터 지향적 URL을 생성 할 수 있습니다. 동적 경로는 _/pages._ 아래의 디렉토리 구조를 기반으로 생성됩니다.

또한, Nuxt는 다른 곳에서는 사용할 수 없는 페이지 구성 요소에 세 가지 특수 메소드를 추가 할 수도 있습니다. 그들은 _validate()_ , _asyncData()_, _fetch()_ 입니다.

```javascript
// pages/index.vue

export default {

  validate() {
    // dynamic URL 인자 값을 검증합니다.
    // 데이터의 가용성을 확인한다.
  },

  asyncData() {
    // component 데이터를 설정(set)합니다.
  },

  fetch() {
    // 구성 요소 데이터를 설정하지 않지만, 추가적으로 컨텍스트 데이터를 가져옵니다.  
  }

}
```

#### **2. Layout components (/layouts)**

레이아웃 컴퍼넌트는 어플리케이션의 구조적 측면(체계)을 강화합니다. 모든 페이지에 있는 공통 컴퍼넌트가 여기에 올려지게(found on) 됩니다 (기본 메뉴, 보조 메뉴, 머리글, 바닥 글 등). 그것들은 _/layouts_ 디렉토리에 있습니다.

당신이 생각하는데로(creative) 이 공간을 꾸밀 수 있고, 그리고 그 꾸밈의 도구는 Vue.js의 컴퍼넌트 입니다. 레이아웃 매인 컨텐츠 구역에 _\<nuxt/>_ 를 추가하나는 것을 잊지 마세요.

```html
<template>
  <div>
    <nuxt/> /* 선언을 잊지 마세요! */
  </div>
</template>
```

다양한 시나리오에 대응하는 많은 사용자 유형에 맞는 _누가 무엇을 볼지(who-sees-what)_ 에 대한 기능을 담은 완벽한 레이아웃 컴퍼넌트를 구축하기 위해 _라우트-미들웨어_ 와 _데이터-상태 저장소_ 를 구성하세요(Incorporate). 커스텀 유져 인터페이스를 사용하는 것보다 더 많은 것을 얻을 수 있습니다. (오역주의)

#### **3. Vue.js components (/components)**

이들은 형식적(regular)이지만 다재다능한 Vue 컴퍼넌트들입니다. _/components_ 디렉토리에 만들어집니다. 해당 디랙토리 안의 컴퍼넌트들은 Page 컴퍼넌트와 같은 특별한 방법으로 무언가가 되지 않습니다.

하지만 비즈니스 로직을 구조화하고 구성 할 수 있습니다. 또한 **페이지** 및 **레이아웃** 컴퍼넌트에서 큰 마크업 구조(HTML)을 숨깁니다. 이렇게 하면 코드베이스를보다 쉽게 ​​관리 할 수 ​​있습니다.

이제 자세히 살펴보십시오 - Nuxt 라이프 사이클 다이어그램의 부분 폴더 구조가 이해 되시나요(can you see)?
**힌트:** 저장소 (nuxtServerInit), 라우트 미들웨어 및 페이지 컴퍼넌트들 (validate, asyncData 및 fetch 메소드)

![Nuxt.js Lifecycle Hooks](https://pbs.twimg.com/media/DeDKSyuUwAARbq-.jpg)<blockquote class="twitter-tweet" data-lang="ko"><p lang="en" dir="ltr">Understanding Nuxt.js Lifecycle Hooks... <a href="https://twitter.com/nuxt_js?ref_src=twsrc%5Etfw">@nuxt_js</a> <a href="https://twitter.com/vuejs?ref_src=twsrc%5Etfw">@vuejs</a> ...had ton of fun creating this diagram. ✍️ <a href="https://t.co/nDu1dXRfEF">pic.twitter.com/nDu1dXRfEF</a></p>&mdash; krutiepatel (@KrutiePatel) <a href="https://twitter.com/KrutiePatel/status/1000022559184764930?ref_src=twsrc%5Etfw">2018년 5월 25일</a></blockquote>

### Assets

#### **Webpacked assets (/assets)**

JavaScript 파일, 커스텀 글꼴 및 CSS 파일과 같은 Asset은 파일 형식에 따라 특정 로더(css-loader, file-loader, url-loader etc)를 사용하여 Webpack에서 처리됩니다. 예를 들어 CSS를 _.scss_ 또는 _.less_ 문법으로 작성하면 Webpack은 특정 로더를 사용하여 이러한 파일을 처리하고 브라우저에서 사용할 수있는 컴파일 된 _.css_ 파일을 만들어냅니다.

_nuxt.config.js_ 의 값을 조정하며 Webpack에서 빌드 프로세스의 일부로 assets 폴더의 이미지를 축소 및 최적화하도록 설정 할 수도 있습니다. Webpack이 파일을 처리 한 후 처리 된 항목에 해시 코드를 첨부(_예 : index.4258e3668a44556dd767.js_)하면 동적 자산의 장기간 캐싱과 캐시 무효화에 도움이됩니다.

#### **Static assets (/static)**

변경되지 않는 파일(assets)의 경우 _static_ 폴더에 안전하게 보관할 수 있습니다. Webpack은 static 폴더를 무시하고 static 폴더 안에 것 전부 다 처리하지 않습니다.

### Modules, serverMiddleware and plugins

위의 것들(modules, serverMiddleware, plugins)은 Nuxt 구성으로 (경로 별로) 모두 정의됩니다. 그것들은 Nuxt 어플리케이션 내에서 어디에서도(globally) 접근 가능합니다.

#### **Modules (/modules)**

새로만들어진(fresh) Nuxt 응용 프로그램은 기본적으로 Vue, Vue Router, Vuex, Vue Server Rendered 및 Vue Meta와 함께 미리 패키지되어 있습니다.

하지만 Sitemap, Google Analytics, Progressive Web Apps 등과 같은 것들은 사용할때는 어떻게 해야할까요? 모듈을 사용할 생각이라면 네, 맞습니다. 이것이 Nuxt 모듈의 강점입니다.

#### **serverMiddleware (i.e. /api)**

serverMiddleware는 응용 프로그램의 확장 점으로 간주 될 수 있습니다. serverMiddleware은 [connect 프레임 워크](https://github.com/senchalabs/connect)를 기본 인스턴스로서 접근 할 수 있기 때문에 특별합니다.

Nuxt는 **connect**를 사용하여 애플리케이션을 재공하기 때문에 사용자 정의 함수를 미들웨어로 기본 요청 파이프 라인에 연결할 수 있습니다.

serverMiddleware를 사용하여 다음을 수행 할 수 있습니다.

- 외부 어플리케이션에 연결할 API 엔드 포인트를 생성
- Nuxt 응용 프로그램에서 사용자에게 이메일을 보낼 API 엔드 포인트를 생성
- 어떤식으로든 Nuxt에 도달하기 전에의 요청을 ​​접근하고 수정

serverMiddleware 및 모듈 용으로 미리 채워진 빈 폴더가 없다는 점을 알아두세요. 필요한 경우 만들면 됩니다.

#### **Plugins (/plugins)**

기존의 Vue 믹스인, 필터 또는 directive를 Nuxt 플러그인으로 변환하면 더 열심히 일하게됩니다. Nuxt 설치와 함께 제공되는 _/plugins_ 디렉토리에 해당 파일을 저장합니다.

하지만 대부분의 경우 외부 패키지 또는 Vue 라이브러리를 Nuxt 플러그인으로 추가하게됩니다. _Vue.use()_ 구문을 사용하여 Nuxt에 통합 할 수 있습니다. 내(저자) Nuxt 프로젝트 안에서 항상 사용하는 주요(staple) 플러그인 중 일부는 `Vue Bootstrap`, `form validation`, `font-awesome icon-set`, `axios`입니다.

이게 플러그인 역활의 끝이 아닙니다. 사용자 정의 플러그인을 작성하여 응용 프로그램 루트에 추가 할 수 있습니다. Nuxt 어플리케이션에서 어디서든 접근할수 있도록(globally) 사용할 수 있습니다. 이것은 변형시킨 GreenSock 또는 Scroll-Magic transitions을 프로젝트에 추가하고 Vue _(/components)_ 및 Page _(/pages)_ 구성 요소에서 사용하는 것이 개인적으로 가장 자주사용하는 방법입니다.

#### High-level overview of modules, serverMiddleware and plugins

![](https://pbs.twimg.com/media/DeqXp7PUwAAvMIk.jpg)
<blockquote class="twitter-tweet" data-lang="ko"><p lang="en" dir="ltr">Understanding modules, serverMiddleware and plugins in Nuxt Js Configuration <a href="https://twitter.com/nuxt_js?ref_src=twsrc%5Etfw">@nuxt_js</a> <a href="https://twitter.com/vuejs?ref_src=twsrc%5Etfw">@vuejs</a> ✍️ <a href="https://t.co/QSubHBkECP">pic.twitter.com/QSubHBkECP</a></p>&mdash; krutiepatel (@KrutiePatel) <a href="https://twitter.com/KrutiePatel/status/1002781159997100033?ref_src=twsrc%5Etfw">2018년 6월 2일</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

### Package, bundle and deliver

원하는 기능을 다 만들었다면, _npm run build_ 를 사용하여 어플리케이션을 빌드할 수 있습니다. Nuxt가  어플리케이션을 패키지화합니다.

아래 다이어그램에 표시된 것처럼 _index.js_ 는 _app.js_ 를 가져 오는 메인 진입 점입니다.

![IMAGE;](https://cdn-images-1.medium.com/max/2000/1*5K1HLp5zxlKfwlM7X2BdZw.jpeg)

Nuxt가 코드를 패키지화합니다. Webpack 번들로 코드를 전달합니다.

_App.js_ 는 루트 Vue 인스턴스를 정의합니다. _.nuxt/App.js_ 를 면밀히 살펴보면 Vue 구성 요소 일 뿐입니다.

이 root Vue 인스턴스가 정의되면 클라이언트 구성요소(_client.js_)는 이를 기반으로 새 인스턴스를 만들고 이를 DOM 요소에 마운트 합니다.

사용자는 그들의 브라우저에서 새로운 앱 인스턴스를 보게됩니다. 서버 구성요소(_server.js_)는 (클라이언트의)요청 마다 새로운 앱 인스턴스를 만듭니다.

마지막으로 Webpack은 클라이언트와 서버 측 모두에서 코드가 실행되도록 앱을 묶습니다. 서버 번들은 서버 측을 렌더링하고 클라이언트 번들은 정적 HTML 마크 업을 브라우저에 활성화(hydrates) 시킵니다. 클라이언트 측 데이터 변경에 반응 할 수 있는 다이나믹 DOM(Virtual DOM?) 으로 변환합니다.

Nuxt는이 모든 작업을 자동으로 수행하므로 수동으로 설정을 필요가 없습니다. 복잡성의 많은 부분이 패키징 및 번들의 마지막 두 단계로 진행됩니다. 그러나 Nuxt는 모든 것을 숨깁니다. 그리하여 다른것은 신경쓰지 않고 제품(the final application)의 어플리케이션 코드에 집중할 수 있습니다.

### 결론

Naxt 프레임 워크의 학습 과정에서 애플리케이션 코드 구조에 대한 더 높은 수준의 개요를 통해 한 걸음 더 나아갈 수 있기를 바랍니다.

이것은 Nuxt 애플리케이션에서 모든 것이 어떻게 잘 맞는지 이해하는 데 도움이되는 많은 대체적인 관점 중 하나 입니다.

개인적으로, 이 작은 연습은 저에게 도움이됩니다.

- Nuxt의 기본적인(out-of-the-box) 기능에 대해 프로젝트 요구 사항을 제시
- 이미 사용 가능한 관련 커뮤니티 모듈 및 플러그인 나열, 그리고
- 커스텀 개발이 필요한 남겨진 부분 혹은 복잡한 부분(bits)를 선택하십시오.

For me personally, this little exercise helps me:

- map out the requirements of the project against out-of-the-box Nuxt features
- list relevant community modules & plugins that are already available, and
- pick out the remaining/complex bits that require custom development.

#### **위에서 사용 된 다이어그램의 고해상도 버전과 연결됩니다.**

1. [Nuxt Js lifecycle hooks](http://bit.ly/2xv6PDV)
2. [Understanding modules, serverMiddleware and plugins](http://bit.ly/2sHNieo)
3. [Universal application code in Nuxt.js](http://bit.ly/2MFl23s)

의견, 피드백 또는 보고 싶은 새로운 다이어그램 아이디어에 대한 제안을 언제든지 얻을 수 있습니다 (아래의 설명 섹션 참조).

![IMAGE](https://cdn-images-1.medium.com/max/1600/1*IslxntaSDwDHfcKEieqDng.jpeg)

[https://www.pariksha.io/](https://www.pariksha.io/)

Nuxt에 익숙하지 않다면, "[Why Nuxt Js is the perfect framework for your landing page?](https://codeburst.io/why-nuxt-js-is-perfect-framework-for-your-landing-page-53e214649b88)"에 대한 이전 기사를 확인해보십시오. 그것은 당신에게 더 깊은 Nuxt로 어플리케이션을 개발할 때의 핵심에 대한 통찰력을 줄 것 입니다.

### 아직도 Nuxt를 안 사용하시나요?

![IMAGE](https://cdn-images-1.medium.com/max/1600/1*xV6a7Pxle-OrI10wcbCCeQ.jpeg)

@_achopin이 @vuejsamsterdam에서 “Are you Nuxt?”라고 물었을 때, 저는 "hey… I am Nuxt"라고 생각했습니다.

그리고 관심이 있다면 Moo Printing에서 인쇄하고 배달 해줄 [Nuxt 스티커](https://www.etsy.com/au/shop/CrewShopDesigns) 를 만들었음을 알려드립니다. 또는 [RedBubble](https://www.redbubble.com/people/krutie?asc=u)에서도 주문할 수 있습니다.
