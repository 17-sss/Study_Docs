---
date: '2022-03-01'
title: '[Nomad Coders] NextJS 시작하기 - #1. FRAMEWORK OVERVIEW'
categories: ['JavaScript', 'NextJS']
options: { hide: true }
---

# ✨ [Nomad Coders] NextJS 시작하기

## #0. INTRODUCTION

<div style="margin: 8px 0;">
  <h3 style="font-weight: 700">목차</h3>
  <a href="#00">0. Library vs Framework </a></br>
  <a href="#01">1. Pages</a></br>
  <a href="#02">2. Static Pre Rendering</a></br>
  <a href="#03">3. Routing</a></br>
  <a href="#04">4. CSS Modules</a></br>
  <a href="#05">5. Styles JSX</a></br>
  <a href="#06">6. Custom App</a></br>
  <hr/>
</div>

<h3 id="00">0. Library vs Framework</h3>

<h4 style="font-weight: 700">라이브러리와 프레임워크의 차이</h4>

1. Next.js는 react.js 어플리케이션 빌드에 도움을 줌
   1. Next.js는 실제 제품 단계 수준 (Production-ready)..?
2. 많은 기업들이 Next.js를 도입했음
   1. 넷플릭스 채용, 틱톡, 텐센트, 트위치, 페라리 등..

<h4 style="font-weight: 700">리액트는 라이브러리, nextjs는 프레임워크</h4>

1. 리액트는 **라이브러리**. (자유도가 있음)
   1. Component 폴더를 만들어도 되고, Routes 폴더를 만들어도 되고
      이 폴더를 다른 이름으로 변경해줘도 되고.
   2. _즉, 개발자가 왕이 되버림. (다 개발자한테 달려있음)_
2. nextjs는 **프레임워크**
   1. pages 폴더안에 컴포넌트를 생성하면, 해당 파일명의 이름으로 url path(페이지)가 생성됨
   2. CRA로 만든 React 애플리케이션은 최상단 index.ts를 수정할 수 있었지만  
      nextjs는 수정 불가능.
   3. _즉, 개발자는 게스트가 되어버림. (주도할 수 없음)_

<h4 style="font-weight: 700"># 정리</h4>

1. **라이브러리**는 “사용자가 파일 이름이나 구조를 정하고, 모든 결정을 내림”
2. **프레임워크**는 “파일 이름이나 구조 등을 정해진 규칙에 따라 만들고 따름”
3. 이 둘의 주요 차이점은 “Inversion of Control” (통제의 역전)
   1. **라이브러리**에서 메서드를 호출하면 사용자가 제어할 수 있음
   2. **프레임워크**에서는 제어가 역전되어 프레임워크가 사용자를 호출

<br/><hr/><br/>

<h3 id="01">1. Pages</h3>

1. next.js에서는 기본적으로 **404 페이지**를 제공해줌
   1. 커스터마이징 가능
2. 모든 페이지는 nextjs 프로젝트 폴더의 최상단에 위치한 **pages** 폴더에 작성
   1. 여기에 들어가는 있는 파일의 이름 자체가 페이지가 됨.

<br/><hr/><br/>

<h3 id="02">2. Static Pre Rendering</h3>

<h4 style="font-weight: 700">들어가기 전에..</h4>

1. Next.js는 앱에 있는 페이지들이 미리 렌더링 됨. (정적으로 생성됨)

<h4 style="font-weight: 700">CSR (Client Side Rendering)</h4>

1. **CSR**(Client Side Rendering)
   1. 브라우저가 자바스크립트를 가져와서 Client Side의 자바스크립트가 모든 UI를 만드는 것  
      ⇒ **브라우저에서 유저가 보는 모든 UI를 만드는 것을 의미**
2. **React**에서의 CSR
   1. 보통의 CRA (Create-React-App)으로 만든 React 앱은 CSR로 동작함.
   2. 브라우저가 react.js를 다운받고 개발자가 만든 코드를 다운받았을 때,  
      그 때 react.js는 다른 모든 것들을 렌더링하고 유저들은 해당 페이지의 컨텐츠를 보게 됨
3. **느린 연결 테스트**
   1. 일반적으로 JavaScript를 비활성화 하는 일은 없음.
   2. 크롬의 개발자도구 → network 탭을 활용해서 “CSR”로 동작하는 앱의 렌더링 과정을 살펴 볼 수 있음
      1. **방법:** 크롬 개발자 도구 → network 탭 → throttling → Slow 3G & “Disable cache” 체크
         - 이렇게 테스트했을 때, 흰 화면부터 시작해서 차례대로 요소들이 렌더링됨.
         - 인터넷이 느린 곳에서 사용하게 된다면..? ⇒ 사용자 경험 저하

<h4 style="font-weight: 700">“페이지 소스 보기”를 활용한 비교</h4>

1. **CRA**로 만든 앱의 “페이지 소스 보기”를 누르면...

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="utf-8" />
       <link rel="icon" href="/favicon.ico" />
       <meta name="viewport" content="width=device-width, initial-scale=1" />
       <meta name="theme-color" content="#000000" />
       <meta name="description" content="Web site created using create-react-app" />
       <link rel="apple-touch-icon" href="/logo192.png" />
       <link rel="manifest" href="/manifest.json" />
       <title>React App</title>
       <script defer src="/static/js/bundle.js"></script>
     </head>
     <body>
       <noscript>You need to enable JavaScript to run this app.</noscript>
       <div id="root"></div>
     </body>
   </html>
   ```

   1. `<div id="root"></div>` 부분에 유저가 보게 될 UI가 들어감.
      - 이 부분에 유저가 보는 UI가 이미 들어있을 것 같지만 아님.
      - 사실상 비어있는 div 요소..
   2. 만약에 JavaScript가 없거나 JavaScript가 비활성화 되어있다면,  
      유저는 아무것도 못보게 될 것 같지만 `<noscript>` 요소에 있는 내용을 보게 됨.
   3. **느린 연결 테스트**해봤을 때, 렌더링되는 과정이 보임 (유저 경험 ⬇️)

2. **next.js**로 만든 앱의 소스 만든 앱의 “페이지 소스 보기”를 누르면...

   ```html
   <!DOCTYPE html>
   <html>
     <head>
       <style data-next-hide-fouc="true">
         body {
           display: none;
         }
       </style>
       <noscript data-next-hide-fouc="true"
         ><style>
           body {
             display: block;
           }
         </style></noscript
       >
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width" />
       <meta name="next-head-count" content="2" />
       <noscript data-n-css=""></noscript>
       <script defer="" nomodule="" src="/_next/static/chunks/polyfills.js?ts=1646118648702"></script>
       <script src="/_next/static/chunks/webpack.js?ts=1646118648702" defer=""></script>
       <script src="/_next/static/chunks/main.js?ts=1646118648702" defer=""></script>
       <script src="/_next/static/chunks/pages/_app.js?ts=1646118648702" defer=""></script>
       <script src="/_next/static/chunks/pages/index.js?ts=1646118648702" defer=""></script>
       <script src="/_next/static/development/_buildManifest.js?ts=1646118648702" defer=""></script>
       <script src="/_next/static/development/_ssgManifest.js?ts=1646118648702" defer=""></script>
       <script src="/_next/static/development/_middlewareManifest.js?ts=1646118648702" defer=""></script>
       <noscript id="__next_css__DO_NOT_USE__"></noscript>
     </head>
     <body>
       <div id="__next" data-reactroot="">
         <div>너 만들어지니? <span>하이지니</span></div>
         <!-- ⭐️ 주목 ⭐️-->
       </div>
       <script src="/_next/static/chunks/react-refresh.js?ts=1646118648702"></script>
       <script id="__NEXT_DATA__" type="application/json">
         {
           "props": { "pageProps": {} },
           "page": "/",
           "query": {},
           "buildId": "development",
           "nextExport": true,
           "autoExport": true,
           "isFallback": false,
           "scriptLoader": []
         }
       </script>
     </body>
   </html>
   ```

   1. CRA로 만든 앱과는 다르게 서버에서 그려서 초기 UI의 모습을 보여줌 (주석 참고)
      - next.js에 의해서 초기 UI가 미리 만들어짐. (**pre-rendering**)
      - JavaScript가 비활성 되어 있더라도 초기 UI는 보임!
   2. **느린 연결 테스트**를 해봤을 때, 렌더링되는 과정이 보이지 않음 (유저 경험 ⬆️)

<h4 style="font-weight: 700">Next.js의 특징 (SSR의 특징) / (일부)</h4>

1. **Hydration**
   1. next.js는 react.js를 백엔드에서 동작시켜서 페이지를 미리 만듬  
      그리고 ReactDOM의 **hydrate** 메서드를 사용하여 이벤트 핸들러만 붙여줌.
2. 유저들이 개발자가 만든 웹사이트에 접근하면 이미 무언가 있음. (초기 화면)
   1. 유저들이 코드를 다운받아서 react를 실행시키길 기다리지 않아도 됨
   2. 모든게 다 로딩되면 react.js가 연결됨
3. 구글 검색 엔진과 같은 **SEO (검색 엔진 최적화)에 유용** **✨**

<h4 style="font-weight: 700">ReactDOM의 render 메서드와 hydrate 메서드</h4>

1. **render** 메서드
   1. `ReactDOM.render(element, container[, callback])`
   2. **“DOM에 리액트 컴포넌트를 렌더링”**
      1. 컨테이너의 자식으로 리액트 컴포넌트를 넣어주는데,  
         기존에 이미 렌더링 된 리액트 컴포넌트가 있다면  
         새로 렌더링 하지 않고, 업데이트만 해줌.
      2. 렌더링이 완료되면 세번째 인자로 전달된 콜백이 실행됨  
         (컴포넌트를 렌더링한 후 콜백을 실행)
2. **hydrate** 메서드
   1. `ReactDOM.hydrate(element, container[, callback])`
   2. **“DOM에 리액트 컴포넌트를 렌더링 하지 않고, 이벤트 핸들러만 붙여줌”**
      1. **SSR**(Server Side Rendering)을 해서 이미 마크업이 채워진 경우  
         굳이 **render** 메서드를 사용할 필요가 없을 때 사용.
         - ❗️ 만약에 SSR에 render 메서드 사용하면 또 다시 화면이 그려지는 현상 발생!
      2. **SSR을 하는 경우에는 hydrate로 콜백만 붙여야 함.**
3. **이 메서드들의 사용 예**
   1. **render 메서드**  
      **CSR**(Client Side Rendering)을 하는 경우에,  
      타겟 컨테이너에 리액트 컴포넌트가 렌더링 된적이 없을 것이기에 사용
   2. **hydrate 메서드**  
      **SSR** 프레임워크와 함께 리액트를 사용할 떄에는 hydrate 사용을 고려해야 함

<h4 style="font-weight: 700"># 정리</h4>

1. 지금까지 만들어왔던 React로 만든 앱의 UI는 `<div id="root"></div>` 안에 들어있을 줄 알았는데  
   아니었다니.. 하나를 또 알아감.
   1. 기존의 React는 CSR 기반으로 작동하니까 당연한 걸지도.
2. nextjs는 React로 DOM에 컴포넌트를 렌더링 할 때,  
   **render** 메서드가 아닌 **hydrate 메서드**를 내부적으로 사용

<h4 style="font-weight: 700"># 참고자료</h4>

- React
  - [리액트의 hydration이란?](https://simsimjae.tistory.com/389)
  - [React 공식 - hydrate 메서드](https://ko.reactjs.org/docs/react-dom.html#hydrate)

<br/><hr/><br/>

<h3 id="03">3. Routing</h3>

<h4 style="font-weight: 700">Link 컴포넌트 (앱 내 페이지 이동)</h4>

1. ❗️ Anchor(`<a>`)태그의 `href` 속성을 활용해서 페이지 이동을 하면 안됨. (`<a href="/">Home</a>`)
   1. 이것을 사용해서 페이지 이동을 하게 된다면, 앱이 새로고침되면서 이동됨.
2. 👌 Next.js의 `Link` 컴포넌트의 `href` 속성을 활용해서 페이지 이동 (`<Link href="/">Home</Link>`)

   1. 앱 내의 “클라이언트 사이드 네비게이션”이 가능해짐.

      > 페이지 간 클라이언트 측 경로 전환을 활성화하고,  
      > single-page app 경험을 제공하려면 `Link`컴포넌트 필요

   2. CRA로 생성한 React 앱을 작업할 때,  
      “React-Router-dom”의 Link 컴포넌트를 사용했었는데 이와 같다고 보면 됨.

3. “React-Router-dom”의 Link 컴포넌트와 **차이점**은..

   1. `className` 속성 전달 불가, `style` 속성으로 스타일 적용 불가 등...
   2. `Link` 컴포넌트로 렌더링되는 DOM 요소는?

      ```html
      <!-- 1️⃣ 일반적으로 Link 컴포넌트를 쓸 때 -->
      <Link href="/">Home</Link>
      <a href='/'>Home</a> // 이렇게 렌더링

      <!-- 2️⃣ className 속성을 전달하려할 때 -->
      <Link href="/">
      	<a className='hello'>Home</a>
      </Link>
      <a href="/" class='home'>Home</a> <!-- 이렇게 렌더링 -->
      ```

      1. 다른 속성을 적용하려면 2️⃣ 와 같이 작업해야 함
      2. Next.js의 `Link` 컴포넌트는 오로지 `href` 속성만을 위한 것.

4. **참고**: [Next.js - No HTML link for pages](https://nextjs.org/docs/messages/no-html-link-for-pages)

<h4 style="font-weight: 700">useRouter Hook</h4>

1. `useRouter`
   1. `const router = useRouter();`
   2. 앱의 함수 컴포넌트에서 router객체 내부에 접근할 때 사용  
      (이 hook을 사용하면 location에 관한 정보들을 얻을 수 있음)
   3. useRouter는 React Hook.
      1. 클래스와 함께 사용할 수 없음.  
         (withRouter를 사용하거나 클래스를 함수 컴포넌트로 래핑해야 함)
2. **참고**: [Next.js - useRouter](https://nextjs.org/docs/api-reference/next/router#userouter)

<br/><hr/><br/>

<h3 id="04">4. CSS Modules</h3>

<h4 style="font-weight: 700">들어가기 전에..</h4>

1. 불러온 css 파일의 선택자들을 적용할 때, 보통 `<nav className=”nav”></nav>` 이런 형식으로 적용함

<h4 style="font-weight: 700">CSS Module</h4>

1. **CSS Module** 파일의 네이밍 규칙은 `[이름].module.css`
2. **사용 예시**

   1. CSS Module 파일 (`./components/NavBar.module.css`)

      ```css
      .nav {
        display: flex;
        background-color: tomato;
      }

      .bg--blue {
        background-color: blue;
      }

      .txt--red {
        color: red;
      }
      ```

   2. NavBar 컴포넌트 (`./components/NavBar.tsx`)

      ```tsx
      import { FunctionComponent } from 'react';
      import Link from 'next/link';
      import styles from './NavBar.module.css';

      const NavBar: FunctionComponent = function () {
        return (
          <nav className={styles.nav}>
            {' '}
            {/* 1️⃣ 불러온 css module의 .nav 적용 */}
            {/* 2️⃣ */}
            <span className={`${styles['bg--blue']} ${styles['txt--red']}`}>바보</span>
            &nbsp;
            {/* 3️⃣ */}
            <span className={[styles['bg--blue'], styles['txt--red']].join(' ')}>바아보</span>
            <Link href="/">Home</Link>
            <Link href="/about">About</Link>
          </nav>
        );
      };

      export default NavBar;
      ```

      1. 1️⃣  처럼 css module을 적용하면 됨.
         - 이렇게 적용하게 되면, 유저가 보는 className은 랜덤하게 바뀜
         - 자바스크립트 오브젝트에서의 프로퍼티 형식으로 작성함
         - ❗️ **className** 속성에 **문자열** 형식으로 “nav” 입력한다해도 적용 안됨.
      2. 만약에 다수 className을 적용해야 할 경우에는 2️⃣ 나 3️⃣ 에 적용한 것처럼 사용
         - 즉, 문자열 형식으로 변환하면 됨.

<br/><hr/><br/>

<h3 id="05">5. Styles JSX</h3>

<h4 style="font-weight: 700">styled jsx</h4>

1. `<style>` 태그를 활용한 스타일 적용

   1. `<style jsx>[css 작성]</style>`

   2. **예시**

      ```tsx
      import { FunctionComponent } from 'react';
      import Link from 'next/link';

      const NavBar: FunctionComponent = function () {
        return (
          <nav>
            <Link href="/">
              <a>Home</a>
            </Link>
            <Link href="/about">
              <a className="txt__red">About</a>
            </Link>
            <style jsx>{`
              a {
                color: magenta;
                text-decoration: none;
              }
              .txt__red {
                color: red;
              }
            `}</style>
          </nav>
        );
      };

      export default NavBar;
      ```

      - **`<style>`** 내에 있는 css는 css가 **작성된 컴포넌트** 에만 적용됨
      - 이 스타일들은 오직 작성된 컴포넌트 내부로 범위가 한정됨
      - 만약에 클래스 선택자인 `.txt__red` 가 다른 컴포넌트에 적용되었어도,  
         그 컴포넌트에 정의된 `.txt__red` 가 없다면 아무 스타일도 적용되지 않음  
         (소스를 보면, 그저 class 속성에 쓰여져있기만 함.)

<br/><hr/><br/>

<h3 id="06">6. Custom App</h3>

<h4 style="font-weight: 700">Custom App (_app)</h4>

1. Next.js에서 페이지들을 렌더링 할 때 `pages` 폴더에서 `_app` 라는 이름의 컴포넌트를 먼저 불러옴
2. **코드 예시** (`./pages/_app.tsx`)

   1. **일반**

      ```tsx
      import { AppProps } from 'next/app';

      function App({ Component, pageProps }: AppProps) {
        return <Component {...pageProps} />;
      }

      export default App;
      ```

      - 만약에 특정 페이지를 Next.JS가 렌더링한다면?  
        → `_app.tsx` 파일 내의 **App** 함수에게 전달 (”Component” Prop에 전달)

   2. **활용**

      ```tsx
      import { AppProps } from 'next/app';
      import NavBar from '../components/NavBar';
      import '../styles/globals.css';

      function App({ Component, pageProps }: AppProps) {
        return (
          <>
            <NavBar />
            <Component {...pageProps} />
            <style jsx global>{`
              a {
                color: white;
              }
            `}</style>
          </>
        );
      }

      export default App;
      ```

3. 이것을 사용한다면..
   1. **Global CSS**를 설정할 수 있음!
      1. Next.JS에서는 컴포넌트에서 css를 import한다면 에러 발생 (일반적으로 css import 할 때)
         - ”**Custom App 컴포넌트**” 외 다른 컴포넌트는 Import 불가
         - 일반적인 컴포넌트에 css를 Import 하고 싶다면 반드시 **module** 이어야 함.
   2. 모든 페이지에 공통적으로 적용되는 컴포넌트도 여기서 렌더링하면 됨.

<h4 style="font-weight: 700"># 참고자료</h4>

- [Next.js : Custom App](https://nextjs.org/docs/advanced-features/custom-app)
- [stackoverflow : What TypeScript type should NextJS \_app.tsx Component and pageProps be?](https://stackoverflow.com/questions/64722812/what-typescript-type-should-nextjs-app-tsx-component-and-pageprops-be)
