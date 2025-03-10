## 10.1 객체란?
- 자바스크립트는 객체 기반의 프로그래밍 언어이며, 자바스크립트를 구성하는 거의 모든 것이 객체이다. (원시 값을 제외한 나머지 값(함수, 배열, 정규 표현식 등)은 모두 객체)
- 원시 타입의 값(원시 값)은 변경 불가능한 값(immutable value)이지만, 객체 타입의 값(객체)은 변경 가능한 값(mutable value)이다.
- 객체는 0개 이상의 프로퍼티로 구성된 집합이며, 프로퍼티는 키와 값으로 구성된다.
- 객체는 프로퍼티와 메서드로 구성된 집합체다.
    - 프로퍼티: 객체의 상태를 나타내는 값(data)
    - 메서드: 프로퍼티(상태 데이터)를 참조하고 조작할 수 있는 동작

## 10.2 객체 리터럴에 의한 객체 생성
- C++나 자바 같은 클래스 기반 객체지향 언어는 클래스를 사전에 정의하고 필요한 시점에 new 연산자와 함께 생성자를 호출하여 인스턴스를 생성하는 방식으로 객체를 생성한다.
- 자바스크립트는 프로토타입 기반 객체지향 언어로서 클래스 기반 객체지향 언어와는 달리 다양한 객체 생성 방법을 지원한다.
    - 객체 리터럴
    - Object 생성자 함수
    - 생성자 함수
    - Object.create 메서드
    - 클래스(ES6)
        ```
        // 객체 리터럴
        var person = {
            name: 'Lee',
            sayHello: function () {
                console.log('Hello! My name is ${this.name}.');
            }
        };
        console.log(typeof person); // object
        console.log(person); // {name: "Lee", sayHello: f}
        ```
- 객체 리터럴은 자바스크립트의 유연함과 강력함을 대표하는 객체 생성 방식이다. 객체를 생성하기 위해 클래스를 먼저 정의하고 new 연산자와 함께 생성자를 호출할 필요가 없다. 숫자 값이나 문자열을 만드는 것과 유사하게 리터럴로 객체를 생성한다.
- 객체 리터럴 외의 객체 생성 방식은 모두 함수를 사용해 객체를 생성한다.

## 10.3 프로퍼티
- 객체는 프로퍼티의 집합이며, 프로퍼티는 키와 값으로 구성된다.
    - 프로퍼티 키: 빈 문자열을 포함하는 모든 문자열 또는 심벌 값
    - 프로퍼티 값: 자바스크립트에서 사용할 수 있는 모든 값
- 식별자 네이밍 규칙을 따르지 않는 이름에는 반드시 따옴표를 사용해야 한다.
    ```
    var person = {
        firstName: 'gil-dong', // 식별자 네이밍 규칙을 준수하는 프로퍼티 키
        'last-name': 'Hong'    // 식별자 네이밍 규칙을 준수하지 않는 프로퍼티 키
    }
    ```
- 문자열 또는 문자열로 평가할 수 있는 표현식을 사용해 프로퍼티 키를 동적으로 생성할 경우에는 프로퍼티 키로 사용할 표현식을 대괄호([...])로 묶어야 한다.
    ```
    var obj = {};
    var key = 'hello';

    obj[key] = 'world';

    console.log(obj); // {hello: "world"}
    ```
- 빈 문자열을 프로퍼티 키로 사용해도 에러가 발생하지 않는다.
- 프로퍼티 키에 문자열이나 심벌 값 외의 값을 사용하면 암묵적으로 타입 변환을 통해 문자열이 된다.
- var, function과 같은 예약어를 프로퍼티 키로 사용해도 에러가 발생하지 않는다.
- 이미 존재하는 프로퍼티 키를 중복 선언하면 나중에 선안한 프로퍼티가 먼저 선안한 프로퍼티를 덮어쓴다.
    ```
    var foo = {
        name: 'Lee',
        name: 'Kim'
    };
    console.log(foo); // {name: "Kim"}
    ```

## 10.4 메서드
- 프로퍼티 값이 함수일 경우 일반 함수와 구분하기 위해 메서드라 부른다. 즉, 메서드는 객체에 묶여 있는 함수를 의미한다.
    ```
    var circle = {
        radius: 5, // 프로퍼티
        getDiameter: function() {   // 메서드
            return 2 * this.radius; // this는 circle을 가리킨다.
        }
    };

    console.log(circle.getDiameter()); // 10
    ```

## 10.5 프로퍼티 접근
- 프로퍼티에 접근하는 방법
    - 마침표 프로퍼티 접근 연산자(.)를 사용하는 마침표 표기법
    - 대괄호 프로퍼티 접근 연산자([...])를 사용하는 대괄호 표기법
        ```
        var person = {
            name: 'Lee'
        };

        // 마침표 표기법
        console.log(person.name);    // Lee
        // 대괄호 표기법
        console.log(person['name']); // Lee
        ```
- 대괄호 표기법을 사용하는 경우 대괄호 프로퍼티 접근 연산자 내부에 지정하는 프로퍼티 키는 반드시 따옴표로 감싼 문자열이어야 한다.
- 프로퍼티 키가 식별자 네이밍 규칙을 준수하지 않는 이름, 즉 자바스크립트에서 사용 가능한 유용한 이름이 아니면 반드시 대괄호 표기법을 사용해야 한다.

## 10.6 프로퍼티 값 갱신
- 이미 존재하는 프로퍼티에 값을 할당하면 프로퍼티 값이 갱신된다.
    ```
    var person = {
        name: 'Lee'
    };
    person.name = 'Kim';

    console.log(person); // {name: "Kim"}
    ```

## 10.7 프로퍼티 동적 생성
- 존재하지 않는 프로퍼티에 값을 할당하면 프로퍼티가 동적으로 생성되어 추가되고 프로퍼티 값이 할당된다.
    ```
    var person = {
        name: 'Lee'
    };
    person.age = 20;

    console.log(person); // {name: "Lee", age: 20}
    ```

## 10.8 프로퍼티 삭제
- delete 연산자는 객체의 프로퍼티를 삭제한다.
    ```
    var person = {
        name: 'Lee',
        age: 20
    };
    delete person.age;

    console.log(person); // {name: "Lee"}
    ```

## 10.9 ES6에서 추가된 객체 리터럴의 확장 기능
### 10.9.1 프로퍼티 축약 표현
- ES6에서는 프로퍼티 값으로 변수를 사용하는 경우 변수 이름과 프로퍼티 키가 동일한 이름일 때 프로퍼티 키를 생략할 수 있다. 이때 프로퍼티 키는 변수 이름으로 자동 생성된다.
    ```
    let x = 1, y = 2;
    const obj = { x, y }; // 프로퍼티 축약 표현
    console.log(obj); // {x: 1, y: 2}
    ```
### 10.9.2 계산된 프로퍼티 이름
- 객체 리터럴 내부에서 계산된 프로퍼티 이름으로 프로퍼티 키를 동적 생성할 수 있다.
    ```
    const prefix = 'prop';
    let i = 0;

    const obj = {
        [`${prefix}-${++1}`]: i,
        [`${prefix}-${++1}`]: i,
        [`${prefix}-${++1}`]: i
    };

    console.log(obj); // {prop-1: 1, prop-2: 2, prop-3: 3}
    ```

### 10.9.3 메서드 축약 표현
- ES6에서는 메서드를 정의할 때 function 키워드를 생략한 축약 표현을 사용할 수 있다.
    ```
    const obj = {
        name: 'Lee',
        sayHi() { // 메서드 축약 표현
            console.log('Hi! ' + this.name);
        }
    };

    obj.sayHi(); // Hi! Lee
    ```

