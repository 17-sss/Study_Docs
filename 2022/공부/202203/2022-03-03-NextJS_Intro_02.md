---
date: '2022-03-03'
title: '[Nomad Coders] NextJS 시작하기 - #1. FRAMEWORK OVERVIEW'
categories: ['JavaScript', 'NextJS']
options: { hide: true }
---

# ✨ [Nomad Coders] NextJS 시작하기

## #0. INTRODUCTION

<div style="margin: 8px 0;">
  <h3 style="font-weight: 700">목차</h3>
  <a href="#00">0. Patterns</a></br>
  <a href="#01">1. Fetching Data (Next.js의 Public 폴더)</a></br>
  <a href="#02">2. Redirect and Rewrite</a></br>
  <a href="#03">3. Server Side Rendering (getServerSideProps)</a></br>
  <a href="#05">5. Dynamic Routes</a></br>
  <a href="#06">6. Movie Detail (Link, useRouter 활용)</a></br>
  <a href="#07">7. Catch All (Catch All URL, getServerSideProps 추가)</a></br>
  <a href="#08">8. 404 Pages</a></br>
  <hr/>
</div>

<h3 id="00">0. Patterns</h3>

> ❗️ [TMDB](https://www.themoviedb.org/)에 가입 후 API 키를 받급받아 작업해야하지만, 과정이 복잡한 관계로 **이후 강의부터는 기록만..**

<h4 style="font-weight: 700">Layout 컴포넌트</h4>

1. 보통 너무 큰 `_app`(**Custom App**)을 원하지는 않음, 그러므로 **Layout 컴포넌트**를 생성하는 것  
   (global로 import 해야할 것이 많음)
   1. Google Analytics
   2. SEO (검색 엔진 최적화)
   3. 스크립트 분석
2. **작성 예시**

   `/components/Layout.tsx`

   ```tsx
   import NavBar from './NavBar';

   const Layout: React.FC = ({ children }) => {
     return (
       <>
         <NavBar />
         {children}
       </>
     );
   };

   export default Layout;
   ```

<h4 style="font-weight: 700">Next.js의 제공 컴포넌트 : Head</h4>

1. 만약에 **CRA**를 통해 React 앱을 작성하고 있다면,  
   앱의 `<head>` 부분을 관리하기 위해선 `react helmet` 같은 라이브러리를 받아야함.
   1. 이것은 현재 작업하는 프로젝트와는 별개인 **새로운 컴포넌트**, **코드**, **오류**가 생긴다는 것
2. 이 `Head` 컴포넌트를 페이지마다 쓴다면, 모든 페이지 컴포넌트에 똑같이 써야함. (이러긴 싫잖아?)

   1. 그러므로 아래와 같이 **Seo 컴포넌트**를 작성하여 분리
   2. **작성 예시**

      `/components/Seo.tsx`

      ```tsx
      import Head from 'next/head';

      interface Props {
        title: string;
      }

      const Seo: React.FC<Props> = ({ title }) => (
        <Head>
          <title>{title} | Next Movies</title>
        </Head>
      );

      export default Seo;
      ```

<br/><hr/><br/>

<h3 id="01">1. Fetching Data (Next.js의 Public 폴더)</h3>

<h4 style="font-weight: 700">public 폴더 활용</h4>

1. public에 있는 이미지를 불러올 때
   1. `./public/vercel.svg` 파일이 있을 때 ⇒ `<img src="/vercel.svg" />`
      1. 항상 `../public/vercel.svg` 이러한 형식으로 불러왔지만,  
         **Next.js**에서는 public 폴더 내의 **파일명**만 불러오면 됨.
2. **참고사항**
   1. `<img>` 태그를 이용하여 이미지를 불러오는 것도 가능하나,  
      **Next.js**에서 제공하는 [Image](https://nextjs.org/docs/api-reference/next/image) 컴포넌트를 활용하여 작업하기.

<h4 style="font-weight: 700">API 사용</h4>

1. API를 사용하는 페이지 컴포넌트 예시

   `/pages/index.tsx`

   ```tsx
   import { useEffect, useState } from 'react';
   import Seo from '../components/Seo';

   const API_KEY = '[API Key]';

   const Home: React.FC = () => {
     const [movies, setMovies] = useState<any[] | undefined>();
     useEffect(() => {
       (async () => {
         const { results } = await (
           await fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`)
         ).json();
         setMovies(results);
       })();
     }, []);
     return (
       <div>
         <Seo title="Home" />
         {!movies && <h4>Loading...</h4>}
         {movies?.map((movie) => (
           <div key={movie.id}>
             <h4>{movie.original_title}</h4>
           </div>
         ))}
       </div>
     );
   };

   export default Home;
   ```

<br/><hr/><br/>

<h3 id="02">2. Redirect and Rewrite</h3>

<h4 style="font-weight: 700">Redirect</h4>

1. 만약 유저가 특정 페이지로 접근했을 때, **redirect** 시켜주고 싶을 것.
2. **Next.js**의 `next.config.js` 파일을 수정 (**redirects**: `source`, `destination` 프로퍼티)

   1. **예시 1)** 만약 유저가 **contact**라는 페이지에 접근한다면, **form**이라는 페이지로 이동

      ```jsx
      /** @type {import('next').NextConfig} */
      const nextConfig = {
        reactStrictMode: true,
        async redirects() {
          return [
            {
              source: '/contact',
              destination: '/form',
              permanent: false,
            },
          ];
        },
      };

      module.exports = nextConfig;
      ```

      - `**permanent` **프로퍼티**  
        `permanent` 프로퍼티에 따라 브라우저나 검색엔진이 이 정보를 기억하는지 여부가 결정됨

   2. **예시 2)** 만약 유저가 **contact**라는 페이지에 접근한다면, 완전히 다른 웹사이트인 **네이버**로 이동

      ```jsx
      /** nextConfig의 내부 (나머지 코드 생략) */
      async redirects() {
        return [
          {
            source: "/contact",
            destination: "https://www.naver.com",
            permanent: false,
          },
        ];
      },
      ```

   3. **예시 3)** 유저가 특정 페이지 접속 시, **일부 path만 바뀌고** 유지하고 싶을 때

      ```jsx
      /** nextConfig의 내부 (나머지 코드 생략) */
      async redirects() {
        return [
          {
            source: "/contact/:path",
            destination: "/new-contact/:path",
            permanent: false,
          },
        ];
      },
      ```

   4. **예시 4)** 유저가 특정 페이지 접속 시, **일부 path만 바뀌고** **나머지 모든 path도 유지**시키고 싶을 때

      ```jsx
      /** nextConfig의 내부 (나머지 코드 생략) */
      async redirects() {
        return [
          {
            source: "/contact/:path*",
            destination: "/new-contact/:path*",
            permanent: false,
          },
        ];
      },
      ```

3. 여러 개의 Redirect 옵션을 설정하고 싶다면, 지금 쓴 것들 처럼 똑같이 더 쓰면 됨  
   (배열로 리턴 되는 거 보면 알겠지?)

<h4 style="font-weight: 700">Rewrite</h4>

1. Redirect와 다른 점은 유저를 Redirect 시키긴하지만, **URL은 변하지 않음**
2. **Next.js**의 `next.config.js` 파일을 수정 (**rewrites**: `source`, `destination` 프로퍼티)

   1. **예시 1)** 현재 GET 방식으로 API 키를 입력해서 요청을 보내고 있는데, API 키를 쉽게 볼 수 없도록 수정

      `./next.config.js`

      ```jsx
      /** @type {import('next').NextConfig} */
      const API_KEY = '10923b261ba94d897ac6b81148314a3f';

      const nextConfig = {
        reactStrictMode: true,
        async redirects() {
          return [
            {
              source: '/contact/:path*',
              destination: '/new-contact/:path*',
              permanent: false,
            },
          ];
        },
        async rewrites() {
          // ✨ Rewrites 주목!
          return [
            {
              source: '/api/movies',
              destination: `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`,
            },
          ];
        },
      };

      module.exports = nextConfig;
      ```

      `./pages/index.tsx` (이전 Home 페이지)

      ```jsx
      import { useEffect, useState } from "react";
      import Seo from "../components/Seo";

      const API_KEY = "10923b261ba94d897ac6b81148314a3f";

      const Home: React.FC = () => {
        const [movies, setMovies] = useState<any[] | undefined>();
        useEffect(() => {
          (async () => {
      			const { results } = await (
              await fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`)
            ).json();
            setMovies(results);
          })();
        }, []);
      	return (/* 생략  */);
      };

      export default Home;
      ```

      `./pages/index.tsx` (변경 Home 페이지)

      ```jsx
      import { useEffect, useState } from "react";
      import Seo from "../components/Seo";

      const Home: React.FC = () => {
        const [movies, setMovies] = useState<any[] | undefined>();
        useEffect(() => {
          (async () => {
            const { results } = await (await fetch(`/api/movies`)).json();
            setMovies(results);
          })();
        }, []);
      	return (/* 생략  */);
      };

      export default Home;
      ```

      1. 이처럼 비동기 요청을 보낼 때, API 키를 포함한 주소를 전부 입력하지 않고 축약한 주소로 전송 가능
      2. 크롬의 Network 창으로 API 키 값을 볼 수 있었는데, 이렇게하면 보이지 않고 원활한 작동 가능
      3. 만약에 `./next.config.js`에도 API Key 값이 보이지 않게 하려면 `.env` 파일 작성하여 사용
         - `.env` 파일 .gitignore에 추가는 필수

<br/><hr/><br/>

<h3 id="03">3. Server Side Rendering (getServerSideProps)</h3>

<h4 style="font-weight: 700">getServerSideProps</h4>

1. Next.js는 서버에서 페이지를 그릴 때, React의 initState를 가진 상태로 렌더링됨.
   1. 기본적인 사항들이 렌더링 된 후 클라이언트로 보내게 됨.
2. 만약에 서버에서 추가적으로 처리한 뒤에 클라이언트로 보내야한다면..?
   1. 컴포넌트 **파일**에 `getServerSideProps()` 함수 정의 **(이름 중요)**
3. **사용 예시** (Home 페이지 컴포넌트)

   1. 현재까지는 Home 페이지에 접근하면 데이터를 useEffect를 통해 요청함  
      (이것은 클라이언트단에서 하게 되는 것)
   2. 데이터를 미리 가져오고 싶을 때 (서버단에서 미리 처리) **(코드)**

      `/pages/index.tsx` (**변경 전**)

      ```tsx
      import { useEffect, useState } from 'react';
      import Seo from '../components/Seo';

      const Home: React.FC = () => {
        const [movies, setMovies] = useState<any[] | undefined>();
        useEffect(() => {
          (async () => {
            const { results } = await (await fetch(`/api/movies`)).json();
            setMovies(results);
          })();
        }, []);
        return (
          <div className="container">
            <Seo title="Home" />
            {!movies && <h4>Loading...</h4>}
            {/* 생략 */}
          </div>
        );
      };

      export default Home;
      ```

      `/pages/index.tsx` (**변경 후**)

      ```tsx
      import { useEffect, useState } from 'react';
      import Seo from '../components/Seo';

      interface Props {
        results: any[];
      }

      const Home: React.FC<Props> = ({ results }) => {
        // useEffect 관련 제거
        return (
          <div className="container">
            <Seo title="Home" />
            {!result && <h4>Loading...</h4>}
            {/* 생략 */}
          </div>
        );
      };

      export default Home;

      // ✨ getServerSideProps 작성 (오직 ServerSide에서만 실행)
      export async function getServerSideProps() {
        // 1️⃣
        const { results } = await (await fetch(`http://localhost:3000/api/movies`)).json();
        return {
          props: { results },
        };
      }
      ```

      1. `getServerSideProps`에서 “return 하는 props”는 “컴포넌트의 props”로 들어가게 됨
      2. `getServerSideProps`는 오직 ServerSide에서만 작동
      3. Custom App 컴포넌트에서는? (Home 페이지에 접근할 때의 **동작순서)**
         - Next.js가 Home 페이지 컴포넌트(`./pages/index.tsx`)를 받음
         - CustomApp의 “Component” Prop에 “Home 페이지 컴포넌트”가 전달
         - Home 페이지 컴포넌트에 정의해놓은 `getServerSideProps()` 호출
         - `getServerSideProps`가 반환한 props를 CustomApp의 “pageProps” Prop에 전달
      4. `getServerSideProps` 내에서 비동기 요청 시 url은 전부를 전달해주어야 함 (1️⃣ 참고)
         - “api/movies” ⇒ 이것은 클라이언트단에서 동작함.
         - 서버단에서 동작하도록 하려면 전체 주소를 전달해야 함.

4. 만약에 `getServerSideProps` 내부에서 오류가 발생한다면 **/pages/500.js** 파일이 표시됨.

<h4 style="font-weight: 700"># 정리</h4>

1. Loading이 보이지 않고 바로 데이터를 적용하고 싶다면,  
   `getServerSideProps` 사용하여 데이터를 미리 불러와주면 됨.
   1. 즉, request time에 반드시 데이터를 fetch해와야 하는 페이지를  
      **pre-render**해야 하는 경우에만 `getServerSideProps`를 사용
   2. 이 때의 단점은, 데이터를 가져오는데 오래걸리면 유저가 아무것도 볼 수 없게 될수도..  
      (기본적인 UI를 못불러오는..)
2. 기본적인 UI를 불러오고 데이터를 따로 불러오려면 굳이 `getServerSideProps` 사용하지 않아도.
   1. 즉, 데이터를 **pre-render**할 필요가 없다면 client side에서 데이터를 가져와야 함

<h4 style="font-weight: 700"># 참고자료</h4>

- [Next.js : getServerSideProps](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props)
- [Next.js : Using getServerSideProps to fetch data at request time](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props#using-getserversideprops-to-fetch-data-at-request-time)

<br/><hr/><br/>

<h3 id="05">5. Dynamic Routes</h3>

<h4 style="font-weight: 700">일반적인 라우팅 예시</h4>

1. 페이지가 `/movies` 와 `/movies/all` 이 있다면?
   1. pages 폴더에 **movies** 폴더 생성
   2. movies 폴더 안에 **index.tsx** 생성 (`/movies`에 매핑 됨)
   3. movies 폴더 안에 **all.tsx** 생성 (`/movies/all`에 매핑 됨)

<h4 style="font-weight: 700">다이나믹 라우팅 예시</h4>

> Next.js에 “변수를 포함하는 Dynamic URL이다” 라고 알려주는 유일한 방법은 “대괄호”를 사용하는 것

1. `/movies/[RandomPathName]` 을 인식하게 하려면?
   1. pages 폴더에 **movies** 폴더 생성
   2. movies 폴더 안에 **[변수명].tsx** 생성 (`/movies/[RandomPathName]`에 매핑 됨)
2. **useRouter Hook**을 사용해서 `query` 프로퍼티 내에 **[변수명]**이 \*\*\*\*프로퍼티로 들어감
   1. 이를 활용해서 다양한 처리가 가능할 듯.

<br/><hr/><br/>

<h3 id="06">6. Movie Detail (Link, useRouter 활용)</h3>

<h4 style="font-weight: 700">앱 내 페이지 이동 방법</h4>

1. **Link 컴포넌트**
2. **useRouter Hook**의 `push` 메소드 사용

<h4 style="font-weight: 700">페이지 이동 시, 가져온 데이터를 전달하는 방법</h4>

1. **사용 예시** (Link 컴포넌트, useRouter Hook)

   1. **Link 컴포넌트**

      ```tsx
      <Link
        href={{
          pathname: `/movies/${movie.id}`,
          query: {
            title: movie.original_title,
          },
        }}
        as={`/movies/${movie.id}`}
      >
        <a>{movie.original_title}</a>
      </Link>
      ```

   2. **useRouter Hook** 활용

      ```tsx
      const router = useRouter();
      const onClick = (id: string, title: string) => {
        router.push(
          {
            pathname: `/movies/${id}`,
            query: {
              title,
            },
          },
          `/movies/${id}`,
        );
      };
      ```

2. 사용 예시 모두 `href(url)`, `as` 속성이 들어감.
   1. `href(url)` 는 이동할 **pathname**과 데이터를 **queryString**으로 전달할 때 사용
   2. `as`는 실질적으로 주소창에 어떻게 보일지 설정 (URL 마스킹)
      1. 이 옵션을 설정하면 `href(url)` 의 **pathname**과 **queryString**의 내용이 주소창에 보이지 않음
      2. useRouter(router 객체)의 query 프로퍼티에는 존재.

<br/><hr/><br/>

<h3 id="07">7. Catch All (Catch All URL, getServerSideProps 추가)</h3>

<h4 style="font-weight: 700">Catch All URL</h4>

1. **catch-all URL**은 모든 캐치해내는 URL
2. <a href="#05">5. Dynamic Routes</a> 에서 **[변수명].tsx**로 페이지 컴포넌트를 작성했을 때,  
   URL내의 존재하는 **path**의 위치와 동일해야지만 작동했었음
   1. 다수의 path가 있을 때 한번에 인식하는 방법은 ⇒ “catch-all URL” 사용
3. catch-all URL **사용 예시**
   1. `/movies/[path1/path2/path3/...]` 을 인식하게 하려면?
      1. pages 폴더에 **movies** 폴더 생성
      2. movies 폴더 안에 **[...변수명].tsx** 생성 (앞에 **...** 필수!!**)**  
         (`/movies/[path1/path2/path3/...]`에 매핑 됨)
      3. “useRouter(router 객체)”에 `query` 프로퍼티 내에 값이 존재하는데  
         더 이상 문자열 형식이 아닌 배열 형식으로 들어가게 됨.

<h4 style="font-weight: 700">getServerSideProps 추가 설명 - Context</h4>

1. **useRouter**는 Client Side에서만 작동하는데, 서버에서 URL의 **pathname**들을 가져오고 싶다면?
   1. `getServerSideProps` 를 활용하여 작업 (<a href="#03">3. Server Side Rendering (getServerSideProps)</a>)
      1. getServerSideProps의 첫번째 인자로 Context가 들어감.
      2. 여기서 `query` 프로퍼티의 값을 리턴하여 컴포넌트에서 사용하면 됨!

<br/><hr/><br/>

<h3 id="08">8. 404 Pages</h3>

<h4 style="font-weight: 700">404 페이지</h4>

1. 404 페이지를 만들고 싶다면..
   1. 그냥 pages 폴더에 **404.tsx** 를 작성하면 됨.

<h4 style="font-weight: 700"># 메모</h4>

1. 이 강의는 여기서 끝. 애초에 어떤 프레임워크인지 알았으니, 직접 자료 찾아가며 적용해보기.
