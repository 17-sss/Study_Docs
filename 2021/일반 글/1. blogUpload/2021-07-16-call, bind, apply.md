# 🤔 `call`, `apply`, `bind`?

## **[1]** `Function.prototype.call()`

`call()` 메소드는 주어진 this 값 및 각각 전달된 인수와 함께 함수를 호출

### 1. 구문 및 매개변수

```js
func.call(thisArg[, arg1[, arg2[, ...]]])
```

-   `thisArg`: func 호출에 제공되는 this의 값.
-   `arg1`, `arg2`, `...`: func이 호출되어야 하는 인수

### 2. 예시 코드 및 설명

```js
let person1 = {
    name: 'Son',
};

let person2 = {
    name: 'Kim',
    study: function () {
        console.log(this.name + '이/가 공부를 하고 있습니다.');
    },
};

// ▶︎ 일반 호출 (person2의 study 함수)
person2.study(); // Kim이/가 공부를 하고 있습니다.

// ▶︎ call()을 사용하여 person2.study() 호출
person2.study.call(person1); // Son이/가 공부를 하고 있습니다.
```

-   위 예시 코드를 보면 `call`을 이용하여 person2의 함수를 호출하지만  
    `call` 메소드 첫 번째 매개변수에 person1을 넣어주고 있기에 `this`는 person2가 아닌 person1을 가리키게 됨.

-   `call`의 첫 번째 매개변수 thisArg는 각 함수의 실행문맥의 `this`를 특정하게 지정하는 매개변수임.
    -   두 번째 매개변수부터는 호출할 함수의 인자들이 들어감.
    -   두번째 매개변수부터는 대상함수의 인수 **앞에** 삽입됨

    ❓ 두 번째 매개변수부터는 "호출할 함수의 인자들"을 사용한 예시 코드

    ```js
    let person3 = {
        name: 'Yrano',
    };
    let person4 = {
        name: 'Rano',
        info: function (gender, age) {
            console.log(
                `this.name: ${this.name}, gender: ${gender}, age: ${age}`,
            );
        },
    };

    person4.info.call(person3, 'Unknown', 2000); // this.name: Yrano, gender: Unknown, age: 2000
    ```

    -   이 예시 코드의 `person4.info()`는 2개의 매개변수가 필요  
        `person4`의 name을 기준으로 실행하려면 `person4.info("Unknown", 2000)` 이렇게 해주면 되지만,  
        `person3`의 name을 기준으로 실행하려면 `person4.info.call(person3, 'Unknown', 2000);` 이렇게 실행!

### 3. `apply()`와 차이

-   이 함수 구문은 `apply()`와 거의 동일하지만,  
    `call()`은 **인수 목록**을, (배열은 전개연산자등을 사용하여 값을 넣을 수 있음)  
    반면에 `apply()`는 **인수 배열 하나**를 받는다는 점이 중요한 차이.

## **[2]** `Function.prototype.apply()`

`apply()` 메서드는 주어진 `this` 값과 배열 (또는 유사 배열 객체) 로 제공되는 `arguments` 로 함수를 호출

### 1. 구문 및 매개변수

```js
func.apply(thisArg, [argsArray]);
```

-   `thisArg`: func 호출에 제공되는 this의 값
-   `argsArray`: func이 호출되어야 하는 인수를 지정하는 유사 배열 객체

### 2. 예시 코드 및 설명

```js
let fruit1 = { name: '사과' };
function fruitInfo(price, color) {
    return console.log(`${this.name}은/는 ${price}원이며 ${color}색입니다`);
}

const arrFruit1Info = [2000, '빨간'];
fruitInfo.apply(fruit1, arrFruit1Info); // "사과은/는 2000원이며 빨간색입니다"
```

-   첫 번째 매개변수 thisArg는 `Function.prototype.call()`과 같이 `this`를 지정한다.  
    하지만 `call()`과 다르게 `apply()`는 두 번째 매개변수를 배열 형태로 넣게된다는 차이점이 있다.  
    (배열 또는 유사배열 객체)

### 3. `call()`와 차이

-   이 함수의 구문은 거의 `call()` 구문과 유사.  
     차이점은 `call()` 은 함수에 전달될 인수 리스트를 받는데 비해,  
    `apply()` 는 인수들의 단일 배열을 받음

> call과 apply를 좀 더 구분하자면..  
> - `call`은 다른 관심사의 인자를 여러개 넣을 때  
> - `apply`는 같은 관심사로 묶인 유사배열을 넣을 때  

## **[3]** `Function.prototype.bind()`

`bind()`는 새롭게 바인딩한 함수를 만들고 바인딩한 함수는 원본 함수 객체를 감싸는 함수다.

-   `bind()`는 `call()`, `apply()`와 같이 함수가 가리키고 있는 `this`를 바꾸지만 호출되지는 않는다.  
    따라서 **변수를 할당하여 호출하는 형태로 사용**된다.

### 1. 구문 및 매개변수

```js
func.bind(thisArg[, arg1[, arg2[, ...]]])
```

-   `thisArg`: 바인딩 함수가 타겟 함수의 this에 전달하는 값
-   `arg1`, `arg2`, `...`: func이 호출되어야 하는 인수

### 2. 예시 코드 및 설명

```js
let fruit1 = { name: '사과' };
let fruit2 = {
    name: '바나나',
    fruitInfo: function (price, color) {
        return console.log(`${this.name}은/는 ${price}원이며 ${color}색입니다`);
    },
};

const arrFruit1Info = [4000, '빨간'];

const appleInfo = fruit2.fruitInfo.bind(fruit1, ...arrFruit1Info);
appleInfo(); // "사과은/는 4000원이며 빨간색입니다"
```

-   위에서 설명했듯 첫번째 매개변수엔 `thisArg`  
    두번째 매개변수는 타겟 함수에서 호출되어야 할 인수들.  
    `call`과 같이 두번째 매개변수는 **인수 목록**을 받음  
    (배열이 아닌 일반적인 값 입력. 배열을 두번째 매개변수로 주려면 전개연산자 활용)


---

### 참고자료

-   [함수 호출 call, apply, bind의 차이](https://velog.io/@josworks27/함수호출-call-apply-bind-차이)
-   [mozilla : Function.prototype.call()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
-   [mozilla : Function.prototype.apply()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
-   [mozilla : Function.prototype.bind()](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
