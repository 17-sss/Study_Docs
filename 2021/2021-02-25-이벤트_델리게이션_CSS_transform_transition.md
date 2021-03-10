---
title: '이벤트 델리게이션 & CSS transform / transition'
categories: [TIL, DOM, CSS]
date: 2021-02-25 23:14:21 +0900
tags: [공부, 검색]
comments: true
---

# 이벤트 델리게이션 & CSS transform / transition

## **1**. 이벤트 버블링. 이벤트 델리게이션 기법
<span style="color: skyblue; font-weight: bold">이벤트 버블링</span>   
    이벤트 버블링은 하위태그에서 발생한 이벤트를 상위태그에서 감지하는 것으로   
    `table > tbody > tr > td` 와 같은 태그 계층 구조에서 td에서 발생한 이벤트를   
    table 태그나 tbody 태그 등에서 감지할 수 있게 하는것이 이벤트 버블링.

    
<span style="color: skyblue; font-weight: bold">이벤트 델리게이션 기법을 사용해야 할 때는?</span>   
    만약 많은 요소에 이벤트를 설정해주어야 한다면,   
    브라우저는 이벤트 발생 유무를 감지하기 위해 자원을 소모하는데 이벤트 갯수가 많아질수록   
    모니터링이 많아져 브라우저 성능은 저하하게 되므로 너무 많은 이벤트를 사용하면 안됨!


<span style="color: skyblue; font-weight: bold">이벤트 델리게이션 패턴 적용 예시</span>   

```js
const ul = document.querySelector("ul");
ul.addEventListener("click", (e) => {
    if(e.target.className === "bigItem") {
        alert('나 큰거임')
    } else if (e.target.className === "smallItem") {
        console.log('나 작은거임')
    }
});
```

<br/>

## **2**. CSS transform / transition
<span style="color: skyblue; font-weight: bold">transform</span>  

- rotate 회전   
    각도의 범위는 0~360도. 마이너스 각도는 시계 반대 방향으로 회전   
    ```css
    transform:rotate(45deg);
    ```

- scale 확대/축소   
    이미지(개체)의 크기를 조절할 수 있으며, 비율을 지정하여 크기를 조절하고 n배로 확대 수치 지정/ 소수점도 가능
    ```css
    transform:scale(1.5);
    transform:scaleX(3); //가로의 3배 확대 / Y로 쓰면 세로의 3배 확대
    ```

- skew 기울임   
    주어진 각도로 기울일 수 있으며, X축은 좌/우로 Y축은 상/로 기울이는 효과를 줌   
    - X축 : + 각도는 우측 / - 각도는 좌측
    - Y축 : + 각도는 아래쪽으로 / - 각조는 윗쪽
    ```css
    transform:skewX(+-10deg);
    ```
- translate 위치이동   
    위치를 이동시킬 수 있으며, 좌/우/상/하로 위치 조정   
    scale과 마찬가지로 X축, Y축으로 지정 가능
    ```css
    transform:translate(10px, 20px); 
    transform:translateX(10px);
    ```

<span style="color: skyblue; font-weight: bold">transition</span>   
마우스를 클릭하거나 hover 상태에 변화를 줌   

- transition-property: 색상이나 위치 등 변화의 대상이 되는 css 속성 지정
- transition-duration: 변화할 때 걸리는 시간을 초단위로 지정
- transition-delay: 변화되기 전 시간을 초단위로 지정
- transition-timing-function: 진행시간 (속성값은 animation 속도형태 속성과 동일)

한 줄 정의 예시      
```css
transition: opacity 2s 1s ease;
/* transition-property : opaticy */
/* transition-duration : 2s */
/* transition-delay : 1s */
/* transition-timing-function : ease */
```

<hr/>

### **참고 링크**
- [[javascript] 이벤트 델리게이션 패턴](https://sub0709.tistory.com/14)
- [css3 animation / tranform / transition](https://bbol-world.tistory.com/65)