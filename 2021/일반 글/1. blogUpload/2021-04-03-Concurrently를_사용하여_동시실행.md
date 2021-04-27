---
title: 'Concurrently를 사용하여 동시실행'
categories: [nodejs, npm]
date: 2021-04-03 22:11:54 +0900
tags: [공부, 검색]
comments: true
---

# Concurrently 사용하여 express 서버와 webpack 동시 실행

## Concurrently 모듈

**> 설치**

```sh
# 현재 작업하고 있는 프로젝트만 적용시키고 싶을 때
$ npm i --save concurrently
# 글로벌 설치
$ npm i -g concurrently
```

**> 사용법**  
package.json에서 script 부분에 key명을 명시 후, 값에는 concurrently를 앞에 명시해주고 뒤에 실행시키고 싶은 명령어를 쓰기

-   예시
    ```json
    {
        "scripts": {
            "start": "concurrently \"nodemon app.js\" \"webpack -w\""
        }
    }
    ```
    -   위의 "scripts"에 "start"라는 명령을 등록함. 실행 시 `npm start`
    -   실행화면  
         <img src="https://user-images.githubusercontent.com/33610315/113480648-685d8580-94d0-11eb-9f65-6fad461754f3.png" width=700>

---

### 참고자료

-   [npmjs : concurrently](https://www.npmjs.com/package/concurrently)
-   [[Concurrently] 서버와 클라이언트 동시에 실행시키기!](https://bin-repository.tistory.com/138)
