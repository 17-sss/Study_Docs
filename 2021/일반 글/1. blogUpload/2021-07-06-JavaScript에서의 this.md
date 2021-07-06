# 🤔 JS에서의 this..?

## **[1]** JavaScript에서의 this와 call, apply, bind 메서드

### **1.** this란?

-   `JS`에서 함수의 this 키워드는 다른 언어들과 비교하여 조금 다르게 동작.
-   `strict mode`와 `non-strict mode` 사이에서도 조금 다름.
-   `this`의 값은 함수를 호출하는 방법에 의해 결정
    -   실행하는 동안 할당에 의해 설정될 수 없으며 함수가 호출될 때 마다 다를 수 있음
-   ES5에선 `this`값이 함수가 어떻게 호출되었는지 설정할 수 있는 `bind` 메서드가 나옴

### **2.** this의 이해

-   바인딩이란?  
    `this`의 호출 방식에 따라 `this`가 특정 _객체_ 에 연결됨.
-   `this`의 바인딩 그리고 `call`, `apply`, `bind` 메서드

    -   **1)** 일반 함수 내부에서의 `this`는 글로벌 객체와 바인딩됨

        ```js
        console.log(this === window); // true

        a = 30;
        console.log(window.a); // 30

        function x() {
            return this;
        }

        x() === window; // true
        ```

    -   **2)** 메서드 내부에서의 `this`는 메서드를 호출한 객체와 바인딩

        ```js
        let ranoInfo = {
            name: 'Rano',
            nickName: 'agumon',
            getInfo() {
                return `Name: ${this.name}  NickName: ${this.nickName}`;
            },
        };

        console.log(ranoInfo.getInfo()); // 'Name: Rano  NickName: agumon`
        ```

    -   **3)** 생성자 함수 내부에서 `this`는 생성자 함수가 생성할 인스턴스와 바인딩 됨.

        ```js
        function ranoInfo() {
            (this.name = 'Rano'), (this.nickName = 'agumon');
            this.getInfo = function () {
                return `Name: ${this.name}  NickName: ${this.nickName}`;
            };
        }

        let ranoInfoTmp = new ranoInfo();
        console.log(ranoInfoTmp); // ranoInfo {name: "Rano", nickName: "agumon", getInfo: ƒ}
        ```

    -   **4)** **`Call`**, **`Apply`**, **`Bind`** 메서드 사용 시, 메서드에 **_첫 번째 인수_** 로 전달하는 객체에 바인딩!

        -   `call` & `apply` 메서드는 기본적으로 함수를 호출하는 역할을 함
            -   기존 함수 호출과의 차이점은?  
                 이 메서드들 (`call` & `apply`)을 사용해 함수를 실행하면 함수의 첫 번째 인자로 전달하는 객체에 `this`를 바인딩 할 수 있음.  
                 이를 통해서 유사 배열 arguments 객체에 배열 메서드를 사용할 수 있음.
        -   `bind`는 첫 번째 인자를 `this`에 바인딩하지만 **함수를 실행하지 않음**, _새로운 함수를 반환_

    -   **4-1)** **`Call`**  
        `call`을 사용하면 함수를 실행하고 함수의 첫 번째 인자로 전달하는 값에 `this`를 바인딩

        ```js
        function logInfo(num1, num2, num3) {
            console.log('name:', this.name);
            console.log('gender:', this.gender);
            console.log('money:', num1 + num2 + num3);
        }

        const rano = {
            name: 'Rano',
            gender: 'Unknown',
        };

        logInfo.call(rano, 0, 1, -1);
        /*
        - logInfo의 console.log
            name: Rano
            gender: Unknown
            money: 0    (num1 + num2 + num3)
        */
        ```

    -   **4-2)** **`Apply`**  
        `apply`를 사용하면 함수를 실행하고 함수의 첫 번째 인자로 전달하는 값에 `this`를 바인딩  
        **>** `call`과 차이점은 인자를 배열 형태로 전달하는 것. (인자로 배열 자체가 전달하는 것이 아닌 배열의 요소들 값으로 전달.)

        ```js
        function logInfo(num1, num2, num3) {
            console.log('name:', this.name);
            console.log('gender:', this.gender);
            console.log('money:', num1 + num2 + num3);
        }

        const rano = {
            name: 'Rano',
            gender: 'Unknown',
        };

        const nums = [0, 1, -1];

        logInfo.apply(rano, nums); // 요즘엔 apply 사용안함!!
        logInfo.call(rano, ...nums); // 요즘은 call()과 전개 연산자 활용하여 사용함!
        /*
        - logInfo의 console.log
            name: Rano
            gender: Unknown
            money: 0    (nums -> num1 + num2 + num3)
        */
        ```

    -   **4-3)** **`Bind`**  
        `bind`는 함수의 첫 번째 인자에 `this`를 바인딩한다는 점은 같음.  
        하지만 함수를 실행하지 않고 _새로운 함수를 반환_  
        **>** 반환된 새로운 함수를 실행해야 원본 함수가 실행됨!!

        ```js
        function logInfo(num1, num2, num3) {
            // 원본 함수
            console.log('name:', this.name);
            console.log('gender:', this.gender);
            console.log('money:', num1 + num2 + num3);
        }

        const rano = {
            name: 'Rano',
            gender: 'Unknown',
        };

        const RanoLogInfo = logInfo.bind(rano, 0); // 새로운 함수!
        RanoLogInfo(-1, 1); // RanoLogInfo(-1)(1)은 안댐!!

        /*
        - logInfo의 console.log
            name: Rano
            gender: Unknown
            money: 0    ( 0 | -1, 1 -> num1 + num2 + num3)
        */
        ```

        바로 위 `RanoLogInfo`를 `bind` 함수를 통해 생성할 때 차라리..

        ```js
        // 이것보다는
        const RanoLogInfo = logInfo.bind(rano, 0);
        // 이게 낫지 않을까?
        const RanoLogInfo = logInfo.bind(rano, ...[0, 1, -1]);
        ```

### **3.** 정리

-   **`this`**  
    `this`는 **함수 호출 방식에 따라 동적으로 결정됨**

    -   일반 함수로 호출 -> this는 글로벌 객체
    -   메서드로 호출 -> 이를 호출한 객체
    -   생성자 함수로 호출 -> 생성자 함수가 생성할 인스턴스
    -   `Call`, `Apply`, `Bind` 메서드 사용 -> 메서드에 첫 번째 인수로 전달하는 객체에 바인딩

-   **`Call`**, **`Apply`**, **`Bind`** 메서드
    -   **`Call`** 메서드는 함수를 실행  
        첫번째 인자에 `this`를 바인딩, 이후 값을 함수의 인자로 전달
    -   **`Apply`** 메서드는 함수를 실행  
        첫번째 인자에 `this`를 바인딩, 이후 _값을 배열의 형태_ 로 받아 차례로 함수의 인자로 전달.
    -   **`bind`** 메서드는 함수를 _실행하지 않음_  
        첫번째 인자에 `this`를 바인딩한 _새로운 함수를 반환._
    -   **_각 메서드를 통해 전달할 수 있는 인자의 갯수엔 제한이 없음!!_**

## **[2]** JavaScript에서의 this는 함수를 호출하는 방법에 따라 결정 (예시 정리)

(**_함수를 호출 할 때 마다 this가 가리키는 값이 다를 수 있음_**)

-   **1)** 전역에서 `this`를 호출

    ```js
    console.log(this); // window
    ```

    -   전역에서의 `this`는 기본적으로 `window`를 가르킴.

-   **2)** 함수 안에서 `this`를 호출

    ```js
    function doSomething() {
        return this;
    }

    console.log(doSomething()); // window
    console.log(window.doSomething()); // window
    ```

    -   this를 결정하는 건 함수가 호출 될 때 이루어짐  
        **_(bind 함수 & 화살표 함수 제외)_**
    -   함수를 호출한 객체가 무엇이냐에 따라 `this`가 결정

-   **3)** 메소드에서 `this`를 호출 했을 경우

    ```js
    function doSomething() {
        return this;
    }

    var obj = {
        doSomething: doSomething,
    };

    console.log(doSomething()); // window
    console.log(obj.doSomething()); // obj
    ```

    -   `this`를 결정하는 것은 함수를 호출한 객체가 무엇이냐에 따라 달라짐.  
         `doSomething` 함수를 obj의 메소드로 등록하고 `obj.doSomething()`을 호출 했기 때문에 `this`는 `obj`.
    -   전역에서 선언되었을 경우 함수라 부르고, 객체에 속한 함수를 메소드라고 부름.

    ```js
    var a = 10;
    var obj = {
        a: 20,
        doSomething: function () {
            function func() {
                console.log(this.a); // 10
            }

            func();

            console.log(this.a); //20
        },
    };

    obj.doSomething();
    ```

    -   `doSomething` 메소드 안에서 선언된 `func()` 함수지만 호출해보면 `console.log(this.a)`에 10.  
         (`obj` 객체와 `func` 함수는 아무런 관련이 없는 함수이기에)

    ```js
    var a = 10;
    var obj = {
        a: 20,
        doSomething: function () {
            var self = this; // obj this를 저장

            function func() {
                console.log(self.a); // 20
            }
            func();
            console.log(this.a); //20
        },
    };

    obj.doSomething();
    ```

    -   메소드 내 함수에서 `this`를 가리키려면 `self` 변수에 객체의 `this`를 저장해서 접근

-   **4)** 생성자에서 `this`를 호출

    ```js
    var age = 10;

    function Person() {
        console.log(this.age);
    }

    var p = new Person(); // 출력: undefined / this는 생성한 객체를 가리킴 (아래 설명 참고)
    Person(); // 출력: 10  /   전역 window의 10임
    ```

    -   `Person`함수는 `window`객체에 속한 함수  
         그냥 호출하면 `this.age`는 전역 변수의 `age` 값을 가리키게 됨.
    -   `Person` 함수를 `new` 키워드를 통해 생성하면 새로운 객체가 만들어지면서  
        `this`는 `window`가 아닌 생성한 객체를 가리키게 되고, 함수의 내용은 생성자가 됨.

-   **5)** `bind` 메서드  
    `bind` 메서드는 함수 내의 `this`를 영구적으로 지정하는 메서드.

    ```js
    var a = 10;
    function func() {
        console.log(this.a);
    }
    func(); // 10

    //func함수의 this를 obj 객체로 바인딩.
    var obj = { a: 20 };
    var new_func = func.bind(obj);

    new_func(); // 20

    var obj2 = { a: 30 };
    var new_func2 = new_func.bind(obj2); // obj로 바인딩 된 함수를 다시 obj2로 바인딩.

    // 이미 바인딩 된 함수의 this는 변하지 않음 (다시 바인딩해도 최초로 바인딩한 값을 가르킴)
    new_func2(); // 20
    ```

-   **6)** 화살표 함수  
    화살표 함수에서 `this`는 자신을 감싼 _렉시컬 컨텍스트_. 전역에서는 `window`를 가리킴.

    ```js
    var func = () => {
        console.log(this);
    };
    func(); // window

    //obj의 func 메소드로 대입
    var obj = { func: func };
    obj.func(); // window

    //obj 객체로 this를 바인딩
    var new_func = func.bind(obj);
    new_func(); // window
    ```

    -   `bind`나 화살표 함수는 `this`를 지정하면 변하지 않음.

---

### 참고자료

-   [[JavaScript] this란?](https://velog.io/@realryankim/JavaScript-this란)
-   [[javascript] this 란](https://bamdule.tistory.com/188)
