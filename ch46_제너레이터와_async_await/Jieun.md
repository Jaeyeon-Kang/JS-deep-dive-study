## 46.1 제너레이터란?
- 제너레이터는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수다.

## 46.2 제너레이터 함수의 정의
- function* 키워드로 선언
- 하나 이상의 yield 표현식을 포함
- 이것을 제외하면 일반 함수를 정의하는 방법과 같다.
  ```js
  // 제너레이터 함수 선언문
  function* getDecFunc() {
    yield 1;
  }

  // 제너레이터 함수 표현식
  const getExpFunc = function* () {
    yield 1;
  }

  // 제너레이터 메서드
  const obj = {
    * getObjMethod() {
      yield 1;
    }
  };

  // 제너레이터 클래스 메서드
  class MyClass {
    * genClsMethod() {
      yield 1;
    }
  }
  ```
- 애스터리스크(*)의 위치는 function 키워드와 함수 이름 사이라면 어디든지 상관 없지만 일관성을 유지하기 위해 function 키워드 바로 뒤에 붙이는 것을 권장한다.
  ```js
  function* getFunc() { yield 1; } // 권장
  function * getFunc() { yield 1; }
  function *getFunc() { yield 1; }
  function*getFunc() { yield 1; }
  ```
- 제너레이터 함수는 화살표 함수로 정의할 수 없으며, new 연산자와 함께 생성자 함수로 호출할 수 없다.

## 46.3 제너레이터 객체
- 제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환한다.
- 제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.
- 제너레이터 객체는 next 메서드를 갖는 이터레이터이지만 이터레이터에 없는 return, throw 메서드를 갖는다.
  - next 메서드를 호출하면 제너레이터 함수의 yield 표현식까지 코드 블록을 실행하고 yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.
  - return 메서드를 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.
    ```js
    function* getFunc() {
      try {
        yield 1;
        yield 2;
        yield 3;
      } catch (e) {
        console.error(e);
      }
    }

    const generator = getFunc();

    console.log(generator.next()); // {value: 1, done: false}
    console.log(generator.return('End!')); // {value: "End!", done: true}
    ```
  - throw 메서드를 호출하면 인수로 전달받은 에러를 발생시키고 undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.
    ```js
    console.log(generator.throw('Error!')); // {value: undefined, done: true}
    ```

## 46.4 제너레이터의 일시 중지와 재개
- 제너레이터 객체의 next 메서드를 호출하면 yield 표현식까지 실행되고 일시 중지된다. 이때 함수의 제어권이 호출자로 양도된다.
- 제너레이터 객체의 next 메서드는 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환한다.
  ```js
  function* getFunc() {
    yield 1;
    yield 2;
    yield 3;
  }

  const generator = genFunc();

  console.log(generator.next()); // {value: 1, done: false}
  console.log(generator.next()); // {value: 2, done: false}
  console.log(generator.next()); // {value: 3, done: false}
  console.log(generator.next()); // {value: undefined, done: true}
  ```
- 제너레이터 객체의 next 메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당된다.
  ```js
  function* genFunc() {
    // 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
    // x 변수의 값은 next 메서드가 두 번째 호출될 때 결정된다.
    const x = yield 1;
    const y = yield (x + 10);
    return x + y;
  }

  const generator = genFunc(0);

  // 처음 호출하는 next 메서드에는 인수를 전달하지 않는다.
  // 만약 처음 호출하는 next 메서드에 인수를 전달하면 무시된다.
  let res = generator.next();
  console.log(res); // {value: 1, done: false}

  // next 메서드에 인수로 전달한 10은 getFunc 함수의 x 변수에 할당된다.
  res = generator.next(10);
  console.log(res); // {value: 20, done: false}

  // next 메서드에 인수로 전달한 20은 getFunc 함수의 y 변수에 할당된다.
  res = generator.next(20);
  console.log(res); // {value: 30, done: true}
  ```

## 46.5 제너레이터의 활용
### 46.5.1 이터러블의 구현
- 제너레이터를 사용한 무한 피보나치 수열 생성 함수
  ```js
  const infiniteFibonacci = (function* () {
    let [pre, cur] = [0, 1];

    while (true) {
      [pre, cur] = [cur, pre + cur];
      yield cur;
    }
  }());

  for (const num of infiniteFibonacci) {
    if (num > 10000) break;
    console.log(num); // 1 2 3 5 8 ... 2584 4181 6765
  }
  ```
### 46.5.2 비동기 처리
- 제너레이터 함수를 사용하여 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있지만, 코드가 장황해지고 가독성도 나빠지기 때문에 async/await를 사용하는 것이 좋다.

## 46.6 async/await
- async/await는 프로미스를 기반으로 동작한다.
- 프로미스 후속 처리 메서드(then/catch/finally) 없이 마치 동기 처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있다.
  ```js
  const fetch = require('node-fetch');

  async function fetchTodo() {
    const url = 'https://jsonplaceholder.typicode.com/todos/1';

    const response = await fetch(url);
    const todo = await response.json();
    console.log(todo);
    // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
  }

  fetchTodo();
  ```

### 46.6.1 async 함수
- async 함수는 async 키워드를 사용해 정의하며 언제나 프로미스를 반환한다.
- await 키워드는 반드시 async 함수 내부에서 사용해야 한다.
  ```js
  // async 함수 선언문
  async function foo(n) { return n; }
  foo(1).then(v => console.log(v)); // 1

  // async 함수 표현식
  const bar = async function (n) { return n; };
  bar(2).then(v => console.log(v)); // 2

  // async 화살표 함수
  const baz = async n => n;
  baz(3).then(v => console.log(v)); // 3

  // async 메서드
  const obj = {
    async foo(n) { return n; }
  };
  obj.foo(4).then(v => console.log(v)); // 4

  // async 클래스 메서드
  class MyClass {
    async bar(n) { return n; }
  }
  const myClass = new MyClass();
  myClass.bar(5).then(v => console.log(v)); // 5
  ```
- 클래스의 constructor 메서드는 인스턴스를 반환해야 하지만 async 함수는 언제나 프로미스를 반환해야 하기 때문에 async 메서드가 될 수 없다.

### 46.6.2 await 키워드
- await 키워드는 프로미스가 settled 상태가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환한다.
- await 키워드는 반드시 프로미스 앞에서 사용해야 한다.
  ```js
  const fetch = require('node-fetch');

  const getGithubUserName = async id => {
    const res = await fetch(`https://api.github.com/users/${id}`);
    const { name } = await res.json();
    console.log(name); // Ungmo Lee
  };

  getGithubUserName('ungmo2');
  ```

### 46.6.3 에러 처리
- 45.1.2에서 본 것 같이 비동기 처리를 위한 콜백 패턴의 단점 중 하나는 try...catch 문으로 에러를 캐치할 수 없다는 것이다.
- 콜백 함수를 인수로 전달받는 비동기 함수와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확하여 async/await 에서 에러 처리는 try...catch 문을 사용할 수 있다.
  ```js
  const fetch = require('node-fetch');

  const foo = async () => {
    try {
      const wrongUrl = 'https://wrong.url';

      const response = await fetch(wrongUrl);
      const data = await response.json();
      console.log(data);
    } catch (err) {
      console.error(err); // TypeError: Failed to fetch
    }
  };

  foo();
  ```
- foo 함수의 catch 문은 HTTP 통신에서 발생한 네트워크 에러뿐 아니라 try 코드 블록 내의 모든 문에서 발생한 일반적인 에러까지 모두 캐치할 수 있다.
- async 함수 내에서 catch 문을 사용해서 에러 처리를 하지 않으면 발생한 에러를 reject하는 프로미스를 반환하기 때문에 Promise.prototype.catch 후속 처리 메서드를 사용해 에러를 캐치할 수도 있다.
  ```js
  const fetch = require('node-fetch');

  const foo = async () => {
    const wrongUrl = 'https://wrong.url';

    const response = await fetch(wrongUrl);
    const data = await response.json();
    return data;
  };

  foo()
    .then(console.log)
    .catch(console.error); // TypeError: Failed to fetch
  ```