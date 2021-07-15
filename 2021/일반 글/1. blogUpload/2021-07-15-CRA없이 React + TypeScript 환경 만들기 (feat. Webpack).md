# CRA없이 React + TypeScript 환경 만들기 (feat. Webpack)

코드스쿼드 과정에서 분명 만들었었는데 난 이해를 못했어서 정리!  
보일러플레이트 다시 만들어보며 메모

## **[1]** 일반 (Normal : No webpack-dev-server)

### 1. 폴더 생성 및 기본 설정

```sh
# webpack-react-typescript-boilerplate이란 이름을 가진 폴더 생성
mkdir webpack-react-typescript-boilerplate

# 생성한 webpack-react-typescript-boilerplate로 이동
cd webpack-react-typescript-boilerplate

# package.json 생성 (-y를 사용하여 바로 생성)
npm init -y

# react, react-dom 패키지 설치
npm i react react-dom

# 개발전용으로 패키지 설치
## 설치목록: typescript, ts-loader, webpack, webpack-cli, @types/react, @types/react-dom, @types/webpack
npm i --save-dev typescript ts-loader webpack webpack-cli @types/react @types/react-dom @types/webpack
```

**1)** 생성된 `package.json`

```json
{
    "name": "webpack-react-typescript-boilerplate",
    "version": "0.1.0",
    "scripts": {
        "build": "webpack",
        "production": "NODE_ENV=production webpack --progress"
    },
    "repository": {
        "type": "git",
        "url": "git+https://github.com/17-sss/webpack-react-typescript-boilerplate.git"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "bugs": {
        "url": "https://github.com/17-sss/webpack-react-typescript-boilerplate/issues"
    },
    "homepage": "https://github.com/17-sss/webpack-react-typescript-boilerplate#readme",
    "description": "",
    "devDependencies": {
        "@types/react": "^17.0.14",
        "@types/react-dom": "^17.0.9",
        "@types/webpack": "^5.28.0",
        "ts-loader": "^9.2.3",
        "typescript": "^4.3.5",
        "webpack": "^5.44.0",
        "webpack-cli": "^4.7.2"
    },
    "dependencies": {
        "react": "^17.0.2",
        "react-dom": "^17.0.2"
    }
}
```

### 2. `tsconfig.json` 파일 생성

```json
{
    "compilerOptions": {
        "target": "es5",
        "module": "esnext",
        "jsx": "react",
        "sourceMap": true,
        "outDir": "./dist/",
        "noImplicitAny": true,
        "lib": ["dom", "DOM.Iterable", "ESNext"],
        "skipLibCheck": true,
        "esModuleInterop": true,
        "allowSyntheticDefaultImports": true,
        "strict": true,
        "forceConsistentCasingInFileNames": true,
        "noFallthroughCasesInSwitch": true,
        "moduleResolution": "node",
        "isolatedModules": true,
        "resolveJsonModule": true,
        "noEmit": true,
        "allowJs": true
    },

    /* ❓ include & exclude 옵션: 컴파일에 포함할 디렉토리/파일 경로를 설정하거나 제외 */
    "include": ["src/**/*"],
    "exclude": ["node_modules/"]
}
```

-   여기서 `compilerOptions`에 쓰인 옵션들 설명
    -   `target`  
        CMA Script 버전을 'ES3'(기본값),  
        'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2019', 'ES2021' 또는 'ESNEXT'로 지정.
    -   `module`  
        모듈을 위한 코드 생성 설정:  
        'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', 'es2020', or 'ESNext'.
    -   `jsx`: JSX 코드 생성 설정: 'preserve', 'react-native', 혹은 'react'.
    -   `sourceMap`: '.map' 파일 생성 여부.
    -   `outDir`: 해당 디렉토리로 결과 구조를 보냅니다.
    -   `noImplicitAny`: 'any' 타입으로 구현된 표현식 혹은 정의 에러처리 여부
    -   `lib`: 컴파일에 포함될 라이브러리 파일 목록
    -   `skipLibCheck`: 정의 파일의 타입 확인을 건너 뛸 지 여부
    -   `esModuleInterop`  
        모든 imports에 대한 namespace 생성을 통해  
        CommonJS와 ES Modules 간의 상호 운용성이 생기게할 지 여부,  
        'allowSyntheticDefaultImports'를 암시적으로 승인.
    -   `allowSyntheticDefaultImports`  
        default export이 아닌 모듈에서도 default import가 가능하게 할 지 여부,  
         해당 설정은 코드 추출에 영향은 주지 않고, 타입확인에만 영향을 줌.
    -   `strict`: 모든 엄격한 타입-체킹 옵션 활성화 여부
    -   `forceConsistentCasingInFileNames`: 같은 파일에 대한 일관되지 않은 참조를 허용하지 않을 지 여부
    -   `noFallthroughCasesInSwitch`: switch문에서 fallthrough 케이스에 대한 에러보고 여부
    -   `moduleResolution`: 모듈 해석 방법 설정: 'node' (Node.js) 혹은 'classic' (TypeScript pre-1.6).
    -   `isolatedModules`: 각 파일을 분리된 모듈로 트랜스파일 ('ts.transpileModule'과 비슷합니다).
    -   `resolveJsonModule`  
        node 프로젝트에서 일반적으로 사용되는 확장자가 '.json'인 모듈을 가져올 수 있음.  
         여기에는 import정적 JSON 모양을 기반으로 하는 유형 생성이 포함됨.  
         (TypeScript는 기본적으로 JSON 파일 확인을 지원하지 않음)
    -   `noEmit`: 결과 파일 내보낼지 여부
    -   `allowJs`: 자바스크립트 파일 컴파일 허용 여부
-   더 많은 옵션: [tsconfig.json의 컴파일 옵션 정리](https://geonlee.tistory.com/214)

### 3. `webpack.config.js` 파일 생성

**1)** 초기

```js
const path = require('path');
const mode = process.env.NODE_ENV || 'development';

module.exports = {
    mode,
    devtool: mode === 'development' ? 'source-map' : 'hidden-source-map',
    entry: {
        app: path.join(__dirname, 'src', 'index.tsx'),
    },

    output: {
        filename: '[name].js', // 컴파일 될 파일이름. ( [name]은 entry 키의 app을 뜻함 )
        path: path.resolve(__dirname, 'dist'), // 컴파일된 파일이 저장될 폴더
    },

    resolve: {
        extensions: ['.ts', '.tsx', '.js'], // 특정 확장자만 resolve
    },
    module: {
        rules: [
            {
                test: /\.(ts|tsx)$/,
                use: 'ts-loader',
                exclude: /node_modules/,
            },
        ],
    },
};
```

-   여기서 쓰인 옵션들 일부 정리

    -   `mode`  
        mode 속성을 development, production 또는 none으로 설정하면  
        webpack에 내장된 환경별 최적화를 활성화 할 수 있음
    -   `entry`: 엔트리 포인트 설정
    -   `output`  
        생성된 번들을 내보낼 위치와 이 파일의 이름을 지정하는 방법을 webpack에 알려주는 역할.  
         기본 출력 파일의 경우에는 ./dist/main.js로 생성된 기타 파일의 경우에는 ./dist 폴더로 설정
    -   `resolve`  
        module이 resolve 되는 방식을 변경시킨다.  
         _예)_ import 'lodash' 호출시, resolve 옵션은 웹팩이 lodash를 찾는 방법을 변경
    -   `module.rules`: 모듈이 생성 될 때 요청과 일치하는 Rule의 배열

-   `resolve`..?
    -   [github - hoilzz : module resolution](https://github.com/hoilzz/TIL/blob/master/FrontEnd/webpack/%EA%B3%B5%ED%99%88%EB%B2%88%EC%97%AD/module-resolution.md)
    -   [Webpack 공식 - Resolve](https://webpack.kr/configuration/resolve/)

**2)** 테스트를 위해 미리 파일 생성

`src/App.tsx`

```tsx
import React from 'react';
const App = () => <div>Test</div>;
export default App;
```

`src/index.tsx`

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(
    <React.StrictMode>
        <App />
    </React.StrictMode>,
    document.getElementById('root'),
);
```

`src/public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Document <%= env %></title>
    </head>
    <body>
        <div id="root"></div>
    </body>
</html>
```

**3)** webpack: `html-webpack-plugin` 설치

`index.html`을 처리하기 위해 `html-webpack-plugin` 설치

```sh
npm i --save-dev html-webpack-plugin
```

webpack.config.js 파일에 html-webpack-plugin을 추가

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const mode = process.env.NODE_ENV || 'development';

module.exports = {
    mode,
    // ... 중간 생략 ...

    plugins: [
        new HtmlWebpackPlugin({
            template: './src/public/index.html',
            templateParameters: {
                // HTML 파일에서 사용될 변수들
                env: process.env.NODE_ENV === 'production' ? '' : '[DEV]',
            },

            // 번들링된 HTML 파일에서 공백이 제거되고 주석이 삭제됨
            minify:
                process.env.NODE_ENV === 'production'
                    ? {
                          collapseWhitespace: true,
                          removeComments: true,
                      }
                    : false,
            hash: true, // 정적 파일을 불러올 때 쿼리문자열에 웹팩 해쉬값을 추가.
        }),
    ],
};
```

-   `HTMLWebpackPlugin`..?
    -   빌드시 html에 값을 전달하여 html을 변경
    -   html 코드 압축(공백 제거, 주석 제거)
    -   webpack으로 빌드한 번들 파일을 자동으로 html에 추가
-   이 설정들을 테스트 해보기 위해 `package.json` 수정 (`scripts` 추가)
    ```json
    "scripts": {
        "build": "webpack",
        "production": "NODE_ENV=production webpack --progress"
    }
    ```
    -   `--progress`는 빌드 진척률을 보여줌
    -   실행법: `npm run [이름]`  
         예) `npm run production`

**3-1)** webpack: `clean-webpack-plugin` 설치

webpack을 실행할 때 마다 dist 폴더의 모든 항목을 삭제하려면 설치

```sh
npm i --save-dev clean-webpack-plugin
```

webpack.config.js 파일에 clean-webpack-plugin을 추가

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

const mode = process.env.NODE_ENV || 'development';

module.exports = {
    mode,
    // ... 중간 생략 ...

    plugins: [
        new HtmlWebpackPlugin({
            // ... 생략 ...
        }),
        new CleanWebpackPlugin(), // 요고만 추가하면 됨
    ],
};
```

## **[2]** webpack dev server

로컬에서 webpack 실행결과를 확인하기 위해 사용.

-   결과물이 실제 파일로 보이지 않음
-   `webpack`을 커맨드라인에 입력하여 수정할 때 마다 번들링한 파일을 생성해줄 필요가 없음

```sh
npm i --save-dev webpack-dev-server
```

`package.json` 수정 (`scripts`에 추가 및 수정)

```json
"scripts": {
    "start": "NODE_ENV=development webpack serve --progress",
    "production": "NODE_ENV=production webpack --progress"
}
```

-   weback dev server 실행 시, `webpack-dev-server`가 아닌 `webpack serve`로!!  
    (예전에는 `webpack-dev-server`로 실행했었는데 `webpack serve`로 바뀜!)

**1)** `webpack.config.js`에 devServer 옵션 설정

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

const mode = process.env.NODE_ENV || 'development';

module.exports = {
    mode,
    // ... 중간 생략 ...

    plugins: [
        /* ... 생략 ... */
    ],
    devServer: {
        contentBase: path.resolve('./public'),
        hot: true,
        host: 'localhost',
        port: 3000,
        historyApiFallback: true,
        writeToDisk: true,
    },
};
```

-   여기서 쓰인 옵션들 정리

    -   `contentBase`  
        서버에 콘텐츠를 제공할 위치를 알려줌 (정적 파일들)
    -   `hot`  
        hot reload 기능 활성  
        (전체 Reload 없이 애플리케이션 실행되는 동안 모듈을 교환, 추가 또는 제거)
    -   `host`: 개발환경에서 도메인을 맞추어야 하는 상황에서 사용
    -   `port`: 개발 서버 포트 번호 설정
    -   `historyApiFallback`  
        히스토리 API를 사용하는 SPA 개발시 설정. 404가 발생하면 index.html로 리다이렉트
    -   `writeToDisk`  
        true시 devServer로 생성되는 컴파일된 파일을 저장함.  
        저장은 설정한 output.path 디렉토리에 기록 (기본값 false)

전부는 아니지만 추가 옵션 끄적끄적 👀

-   추가 옵션 1

    -   `publicPath`: 브라우저를 통해 접근하는 경로 (기본값 '/')
    -   `overlay`: 빌드시 에러나 경고를 브라우져 화면에 표시
    -   `stats`  
         메시지 수준을 정할수 있다.  
         'none', 'errors-only', 'minimal', 'normal', 'verbose'로 메세지 수준을 조절

-   추가 옵션 2 (lazy & filename)

    -   `lazy`  
         lazy모드가 활성화가 되면 dev-server는 요청될 때만 번들을 컴파일.  
         이는 웹 팩이 파일 변경 사항을 감시하지 않음을 의미.  
         게으른 모드라고도 불러짐
    -   `filename`  
        devServer 설정하는 부분에서의 filename 옵션은 lazy 모드에서 사용하면 컴파일을 줄일수 있음.  
         **_lazy 모드에서 없이 사용하면 효과 없음_**

        ```js
        // 적용 예시
        devServer: {
            lazy: true,
            filename: '[name].js',
        }
        ```

## **[3]** webpack에 loader 추가

**1-1)** `css-loader`  
webpack에서 .css 파일을 읽어들이기위해 사용하는 로더

```sh
# css-loader 설치
npm i --save-dev css-loader
```

**1-2)** `style-loader`  
`<style>` 태그를 삽입하여 CSS에 DOM을 추가

```sh
# style-loader 설치
npm i --save-dev style-loader
```

**1-3)** `css-loader` & `style-loader`를 `webpack.config.js`에 추가

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

const mode = process.env.NODE_ENV || 'development';

module.exports = {
    mode,
    devtool: mode === 'development' ? 'source-map' : 'hidden-source-map',
    entry: {
        /* 생략 */
    },
    resolve: {
        /* 생략 */
    },
    module: {
        rules: [
            {
                test: /\.(ts|tsx)$/,
                use: 'ts-loader',
                exclude: /node_modules/,
            },
            { test: /\.css$/, use: ['style-loader', 'css-loader'] }, // 추가('style-loader', 'css-loader')
        ],
    },
    output: {
        /* 생략 */
    },
    plugins: [
        /* 생략 */
    ],
    devServer: {
        /* 생략 */
    },
};
```

**1-4)** `css-loader` & `style-loader` 추가 설명 및 동작 원리

-   이 2개의 loader들은 보통 결합해서 씀.
-   동작 원리
    -   `use: ['style-loader', 'css-loader']` 처럼  
         여러 loader 를 위와 같이 중복해서 쓰면 오른쪽의 loader 부터 읽게 됨.  
         이러한 것을 **ChainingLoaders** 라고 한다.
        ```
        1. Webpack은 모듈안에 의존적인 css 파일을 검색
        2. 찾으면 css-loader 실행
        3. css-loader는 모든 css와 그 css 내부의 import한 다른 css 파일을
            json 파일로 로드하고 style-loader에 넘겨준다.
        4. style-loader는 json을 가져와서 <style> 태그를 추가하고
            index.html 파일 안에 tag를 삽입한다
        ```

**2-1)** `file-loader`  
이름 그대로 파일을 로딩하는 loader

-   웹팩은 모든 것을 모듈로 처리하는데 CSS 파일도 모듈로 다룸.  
     CSS에서 url() 함수에 파일명을 지정할수 있는데  
     이를 모듈에서 발견하면 웹팩은 file-loader를 통해 파일을 복사함.

```sh
# file-loader 설치
npm i --save-dev file-loader
```

**2-2)** `file-loader`를 `webpack.config.js`에 추가

```js
/* 생략 */
module.exports = {
    mode,
    devtool: /* 생략 */,
    entry: { /* 생략 */ },
    resolve: { /* 생략 */ },
    module: {
        rules: [
            /* 생략 */

            // 추가 (file-loader)
            {
                test: /\.(webp|jpg|png|jpeg)$/,
                loader: 'file-loader',
                options: {
                    name: '[name].[ext]?[hash]', // 파일명 형식
                },
            },
        ],
    },
    output: { /* 생략 */ },
    plugins: [ /* 생략 */ ],
    devServer: { /* 생략 */ },
};
```

-   여기서는 확장자 `webp`, `jpg`, `png`, `jpeg`를 가진 파일들을 `file-loader`로 처리해줌
-   `options`에서`name`의 placeholder들
    -   `[name]`은 entry에 있는 app의 키 이름을 가져옴 (`"app"`)
    -   `[ext]`는 파일/리소스의 파일 **확장자**
    -   `[hash]`는 특정 빌드에 해당하는 **해쉬** 값
-   file-loader의 속성들
    -   `options`: 옵션을 설정.
    -   `options.name`: 조건에 해당하는 파일이 저장될 경로(이름 포함)를 설정.
    -   `output`: 번들된 결과물이 저장되는 출력 디렉토리에 대한 정보를 담은 객체.
    -   `path`: 파일이 저장될 경로를 의미.

**3)** loader 설정 할 때의 `webpack.config.js`의 `module.rules`의 아이템 (`rule`) 하나마다 속성들.

-   `test`: 정규식 또는 문자열을 통해 조건에 해당하는 파일을 찾음.
-   `use`: 조건에 해당하는 파일을 처리할 로더. 복수의 로더가 있는 경우 역순으로 실행.
-   `include`: 검사를 포함할 경로이며, test와 함께 사용시 대상인 파일은 두 조건을 모두 일치해야 함.
-   `exclude`: 검사를 제외할 경로이며, test 및 include 보다 우선시 함.

---

### 참고자료

**[+]** 공통

-   [webpack : module.rules](https://webpack.kr/configuration/module/#modulerules)
-   [webpack : loaders inline 접두사](https://webpack.kr/concepts/loaders/#inline)

**[1]** 일반 (Normal : No webpack-dev-server)

-   [CRA 없이 React + TypeScript + Webpack 프로젝트 만들기 #1](https://blog.doitreviews.com/development/2020-05-07-react-typescript-webpack/)
-   [TypeScript컴파일 설정](https://yamoo9.gitbook.io/typescript/cli-env/tsconfig)
-   [Webpack 공식](https://webpack.kr/)
-   [[나만의 웹팩 만들기] 9. resolve](https://hoilzz.github.io/webpack/9-resolve/)
-   [tsconfig - resolveJsonModule](https://www.typescriptlang.org/tsconfig#resolveJsonModule)
-   [자주 사용하는 플러그인 - 웹팩(Webpack) 기본편 | 김정환](https://agal.tistory.com/71)

**[2]** webpack dev server

-   [CRA 없이 React + TypeScript + Webpack 프로젝트 만들기 #2](https://blog.doitreviews.com/development/2020-05-11-webpack-dev-server-and-svg/)
-   [웹팩 핸드북 : 웹팩 데브 서버](https://joshua1988.github.io/webpack-guide/tutorials/webpack-dev-server.html#웹팩-데브-서버)
-   [프론트엔드 개발환경의 이해: 웹팩(심화)](https://jeonghwan-kim.github.io/series/2020/01/02/frontend-dev-env-webpack-intermediate.html)
-   [webpack 설정 option에 대해서](https://trustyoo86.github.io/webpack/2018/01/10/webpack-configuration.html)
-   Webpack 공식 : devServer의 옵션들 일부
    -   [devServer](https://webpack.js.org/configuration/dev-server/)
    -   [devServer.lazy](https://webpack.js.org/configuration/dev-server/#devserverlazy-)
    -   [devServer.filename](https://webpack.js.org/configuration/dev-server/#devserverfilename-)
    -   [devServer.writeToDisk](https://webpack.js.org/configuration/dev-server/#devserverwritetodisk-)
    -   [devServer.hot](https://webpack.js.org/configuration/dev-server/#devserverhot)
        -   [webpack : Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/)

**[3]** webpack에 loader 추가

-   [[Webpack] css-loader & style-loader](https://heecheolman.tistory.com/33)
-   [웹팩의 file-loader와 url-loader](https://jeonghwan-kim.github.io/js/2017/05/22/webpack-file-loader.html)
-   [webpack : file-loader Placeholders](https://v4.webpack.js.org/loaders/file-loader/#ext)
-   [Webpack(웹팩) 캐싱 전략](https://joshua1988.github.io/web-development/webpack/caching-strategy/)
-   [우럭🐟 의 웹팩 기초, 로더](https://kowoohyuk.tistory.com/139)
