

## 제너레이터란

* 제너레이터(generator)는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수이다.

#### 제너레이터와 일반 함수의 차이

* 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다.
* 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다.
* 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.

---

## 제너레이터 함수의 정의

* 제너레이터 함수는 `function*` 키워드로 선언하며, 하나 이상의 yield 표현식을 포함해야 한다.
* 애스터리스크(\*)의 위치는 function 키워드와 함수 이름 사이라면 어디든 상관없지만, 일반적으로 function 키워드 바로 뒤에 붙여서 사용한다.
* 제너레이터 함수는 화살표 함수로 정의할 수 없다.
* 제너레이터 함수는 new 연산자와 함께 생성자 함수로 호출할 수 없다.

```js
// 제너레이터 함수 선언문
function* genDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genExpFunc = function*() {
  yield 1;
}

// 제너레이터 메서드
const obj = {
  * genObjMethod() {
    yield 1;
  }
}

// 제너레이터 클래스 메서드
class MyClass {
  * genClsMethod() {
    yield 1;
  }
}
```

---

## 제너레이터 객체

* 제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하지 않고, 제너레이터 객체를 생성해 반환한다.
* 제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 동시에 이터레이터이다.
* 제너레이터 객체는 Symbol.iterator 메서드를 상속받는 이터러블이면서 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 next 메서드를 소유하는 이터레이터이다.
* 제너레이터 객체는 next 메서드를 갖는 이터레이터이지만, 이터레이터에는 없는 return, throw 메서드를 가진다.

```js
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
const generator = genFunc();

// 제너레이터 객체는 이터러블이면서 동시에 이터레이터이다.
// 이터러블은 Symbol.iterator 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 객체이다.
console.log(Symbol.iterator in generator); // true

// 이터레이터는 next 메서드를 가진다.
console.log('next' in generator); // true
```

* next 메서드를 호출하면 제너레이터 함수의 yield 표현식까지 코드 블록을 실행하고, yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.
* return 메서드를 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.
* throw 메서드를 호출하면 인수로 전달받은 에러를 발생시키고, undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.

```js
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  }
  catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

// next 메서드
console.log(generator.next()); // {value: 1, done: false}

// return 메서드
console.log(generator.return('End!')); // {value: 'End!', done: true}

// throw 메서드
console.log(generator.throw('Error!')); // {value: undefined, done: true}
```

---

## 제너레이터의 일시 중지와 재개

* 제너레이터는 yield 키워드와 next 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 재개할 수 있다.
* 제너레이터 객체의 next 메서드를 호출하면 제너레이터 함수의 코드 블록을 yield 표현식까지만 실행한다.
* yield 키워드는 제너레이터 함수의 실행을 일시 중지시키거나, yield 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환한다.

```js
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 next 메서드를 가진다.
const generator = genFunc();

// 첫 번째 next()
console.log(generator.next()); // {value: 1, done: false}

// 두 번째 next()
console.log(generator.next()); // {value: 2, done: false}

// 세 번째 next()
console.log(generator.next()); // {value: 3, done: false}

// 마지막 next()
console.log(generator.next()); // {value: undefined, done: true}
```

* 제너레이터 객체의 next 메서드를 호출하면 yield 표현식까지 실행되고 일시 중지되고, 함수의 제어권이 호출자에게 양도된다.
* 이후 필요한 시점에 호출자가 다시 next 메서드를 호출하면, 일시중지된 코드부터 실행을 재개하여 다음 yield 표현식까지 실행되고 또 다시 일시 중지된다.
* 제너레이터 객체의 next 메서드는 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환한다.
* next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 yield 표현식에서 yield된 값(yield 키워드 뒤의 값)이 할당되고, done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언 값이 할당된다.
* 제너레이터 객체의 next 메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당된다.

```js
function* genFunc() {
  // x 변수에는 아직 아무것도 할당되지 않는다. next 메서드가 두 번째 호출될 때 결정된다.
  const x = yield 1;
  
  // 두 번째 next 메서드를 호출할 때 전달한 인수(20)가 첫 번째 yield 표현식을 할당받는 x 변수에 할당된다.
  const y = yield (x + 10);
  
  // 세 번째 next 메서드를 호출할 때 전달한 인수(70)가 두 번째 yield 표현식을 할당받는 y 변수에 할당된다.
  return x - y;
}

const generator = genFunc(0);

// 첫 번째 next()
let res = generator.next();
console.log(res); // {value: 1, done: false}, x: 할당X, y: 할당X

// 두 번째 next()
res = generator.next(20);
console.log(res); // {value: 30, done: false}, x: 20, y: 할당X

// 마지막 next()
res = generator.next(70);
console.log(res); // {value: -50, done: true}, x: 20, y: 70
```

---

## 제너레이터의 활용

### 이터러블의 구현

* 제너레이터 함수를 사용하면 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단히 이터러블을 구현할 수 있다.

```js
// 무한 피보나치 수열을 생성하는 함수

// 무한 이터러블을 생성하는 제너레이터 함수
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];
  
  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
}());

for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8 ... 2584 4161 6765
}
```

### 비동기 처리

* 제너레이터 함수를 활용해 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현할 수 있다. (프로미스의 후속 처리 메서드 then/catch/finally 없이 비동기 처리 결과를 반환하도록 구현할 수 있다.)
* 제너레이터 실행기가 필요하다면 직접 구현하는 것보다 co 라이브러리를 사용하는 것이 좋다.

```js
// 제너레이터 실행기
const async = generatorFunc => {
  const generator = generatorFunc();
  
  const onResolved = arg => {
    const result = generator.next(arg);
    
    return result.done
      ? result.value
      : result.value.then(res => onResolved(res));
  };
  
  return onResolved;
}

(async(function* fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';
  
  const response = yield fetch(url);
  const todo = yield response.json();
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
})());
```

#### 코드 동작 순서

* async 함수가 호출되면 인수로 전달받은 제너레이터 함수 fetchTodo를 호출하여 제너레이터 객체를 생성하고 onResolved 함수를 반환한다.
* onResolved 함수는 상위 스코프의 generator 변수를 기억하는 클로저이다.
* async 함수가 반환한 onResolved 함수를 즉시 호출하여, 위에서 생성한 제너레이터 객체의 next 메서드를 처음 호출한다.
* next 메서드가 처음 호출되면 제너레이터 함수 fetchTodo의 첫 번째 yield 문까지 실행된다.
* 이때 next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 false라면, 이터레이터 리절트 객체의 value 프로퍼티 값(첫 번째 yield된 fetch 함수가 반환한 프로미스가 resolve한 Response 객체)을 onResolved 함수에 인수로 전달하면서 재귀 호출한다.
* onResolved 함수에 인수로 전달된 Response 객체를 next 메서드에 인수로 전달하면서 next 메서드를 두 번째로 호출한다.
* 이때 next 메서드에 인수로 전달한 Response 객체는 제너레이터 함수 fetchTodo의 response 변수에 할당되고, 제너레이터 함수 fetchTodo의 두 번째 yield 문까지 실행된다.
* next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 false라면, 이터레이터 리절트 객체의 value 프로퍼티 값(두 번째 yield된 response.json 메서드가 반환한 프로미스가 resolve한 todo 객체)을 onResolved 함수에 인수로 전달하면서 재귀 호출한다.
* onResolved 함수에 인수로 전달된 todo 객체를 next 메서드에 인수로 전달하면서 next 메서드를 세 번째로 호출한다.
* 이때 next 메서드에 인수로 전달한 todo 객체는 제너레이터 함수 fetchTodo의 todo 변수에 할당되고, 제너레이터 함수 fetchTodo가 끝까지 실행된다.
* next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 true라면, 이터레이터 리절트 객체의 value 프로퍼티 값(제너레이터 함수 fetchTodo의 반환값인 undefined)을 그대로 반환하고 처리를 종료한다.

---

## async / await

* ES8(ECMAScript 2017)에서는 제너레이터보다 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 async/await가 도입되었다.
* async/await는 프로미스를 기반으로 동작한다.
* async/await를 사용하면 프로미스의 후속 처리 메서드 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있다.

---

## async 함수

* async 함수는 async 키워드를 사용해 정의하며 언제나 프로미스를 반환한다.
* async 함수가 명시적으로 프로미스를 반환하지 않더라도, async 함수는 암묵적으로 반환값을 resolve하는 프로미스를 반환한다.

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

---

## await 키워드

* await 키워드는 프로미스가 settled 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 resolve한 처리 결과를 반환한다.
* await 키워드는 반드시 async 함수 내부에서, 그리고 프로미스 앞에서 사용해야 한다.

```js
const getGithubUserName = async id => {
  const res = await fetch(`https://api.github.com/users/${id}`);
  const { name } = await res.json();
  console.log(name); // Seokyung Jee
};

getGithubUserName('seokyung');
```

---

## 에러 처리

* async/await에서 에러 처리는 try ... catch 문을 사용할 수 있다.
* async 함수 내에서 catch 문을 사용해서 에러 처리를 하지 않으면, async 함수는 발생한 에러를 reject하는 프로미스를 반환하기 때문에 async 함수를 호출하고 Promise.prototype.catch 후속 처리 메서드를 사용해 에러를 캐치할 수 있다.

```js
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

