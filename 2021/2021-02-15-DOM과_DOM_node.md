---
title: 'DOM과 DOM node'
categories: [TIL, DOM]
date: 2021-01-18 16:06:23 +0900
tags: [공부, 검색]
comments: true
---

# DOM과 DOM node

## [1] DOM - Document Object Model
DOM은 웹 페이지에 대한 인터페이스.   
기본적으로 여러 프로그램들이 페이지의 콘텐츠 및 구조   
그리고 스타일을 읽고 조작할 수 있도록 API를 제공

### DOM은 어떻게 생성되는 걸까?
- DOM은 원본 HTML 문서의 객체 기반 표현 방식.   
    둘은 서로 비슷하지만, DOM이 갖고 있는 근본적인 차이는 
    단순 텍스트로 구성된 HTML 문서의 내용과 구조가   
    객체 모델로 변환되어 다양한 프로그램에서 사용될 수 있다.
- DOM의 개체 구조는 _노트 트리_ 로 표현된다.   
    하나의 부모 줄기가 여러 개의 자식 나뭇가지를 갖고 있고,   
    또 각각의 나뭇가지는 잎들을 가질 수 있는 나무와 같은 구조로 이루어져 있기 때문이다.
- _노드 트리_ 예시
    - HTML
        ```html
        <html lang="en">
            <head>
                <title>My first web page</title>
            </head>    
            <body>
                <h1>Hello, world!</h1>
                <p>How are you?</p>
            </body>
        </html>
        ```
    - 이미지   
        <img src="https://user-images.githubusercontent.com/33610315/107915579-160be800-6fa8-11eb-8d43-2d2d26fa44d6.png" width="500"/>
- 추가 정보는 **참고링크의 1번째** 참고

## [2] DOM node
-- 작성중 --

### **참고 링크**
- [(번역) DOM은 정확히 무엇일까?](https://wit.nts-corp.com/2019/02/14/5522)
