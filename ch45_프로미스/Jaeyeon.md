

# 45장 프로미스

자바스크립트는 비동기 처리를 위해 주로 콜백 함수를 사용한다. 하지만 전통적인 콜백 패턴은 코드의 가독성을 떨어뜨리고, 에러 처리가 어렵다는 등 여러 문제점이 있다.
ES6에서는 비동기 처리를 위한 새로운 패턴으로 \*\*프로미스(Promise)\*\*가 도입되었다.

---

## 45.1 비동기 처리를 위한 콜백 패턴의 단점

### 45.1.1 콜백 헬

기본적인 비동기 처리 패턴 예시부터 시작.

#### 예제 45-01: GET 요청을 위한 비동기 함수

```js
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();
  xhr.onload = () => {
    if (xhr.status === 200) {
      console.log(JSON.parse(xhr.response));
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

// id가 1인 post를 조회
get('https://jsonplaceholder.typicode.com/posts/1');
```

* `get` 함수가 서버의 응답 결과를 콘솔에 출력.
* 하지만 이 함수가 서버의 응답 결과를 반환하도록 하려면 어떻게 해야 할까?

#### 콜백 함수의 한계

* 비동기 함수의 내부에서만 결과를 처리할 수 있음.
* 비동기 처리 결과를 함수 외부로 반환하는 게 불가능.

```js
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();
  xhr.onload = () => {
    if (xhr.status === 200) {
      return JSON.parse(xhr.response); // 의미 없음
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

const response = get('https://jsonplaceholder.typicode.com/posts/1');
console.log(response); // undefined
```

* `get` 함수에서 반환하는 값은 항상 `undefined`.
* 비동기 함수에서 값을 외부로 전달할 수 없음.

#### setTimeout의 예시

```js
let g = 0;
setTimeout(() => { g = 100; }, 0);
console.log(g); // 0
```

* setTimeout에서 값이 변경되었지만, 비동기 실행이라 직후에 값을 사용할 수 없음.

---

### 45.1.2 여러 처리의 한계 (콜백 지옥)

콜백 함수를 중첩해서 쓸수록 코드가 복잡해지고 가독성이 떨어짐.

```js
get('/step1', a => {
  get(`/step2/${a}`, b => {
    get(`/step3/${b}`, c => {
      get(`/step4/${c}`, d => {
        console.log(d);
      });
    });
  });
});
```

* 여러 단계의 비동기 처리를 연결하면, 콜백이 중첩되어 코드가 헬처럼 복잡해짐.

---

### 45.1.2 에러 처리의 한계

콜백 패턴에서는 비동기 함수에서 발생하는 에러를 제대로 캐치하지 못함.

#### 예제 45-09

```js
try {
  setTimeout(() => { throw new Error('Error!'); }, 1000);
} catch (e) {
  // 에러가 캐치되지 않음
  console.error('에러 발생!', e);
}
```

* setTimeout 등 비동기 함수 내에서 발생한 에러는 외부 try-catch로 잡을 수 없음.

---

## 45.2 프로미스의 생성

ES6에서 도입된 **Promise 생성자 함수**로 비동기 처리를 좀 더 구조적으로 할 수 있다.

### 프로미스 기본 구조

```js
const promise = new Promise((resolve, reject) => {
  // 비동기 처리
  if (/* 성공 조건 */) {
    resolve('result');
  } else {
    reject('failure reason');
  }
});
```

* `resolve`는 성공 결과를, `reject`는 실패 이유를 반환.

### 예제 45-11: 프로미스를 활용한 GET 요청

```js
const promiseGet = url => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();
    xhr.onload = () => {
      if (xhr.status === 200) {
        resolve(JSON.parse(xhr.response));
      } else {
        reject(new Error(xhr.status));
      }
    };
  });
};

// 사용 예시
promiseGet('https://jsonplaceholder.typicode.com/posts/1');
```

### 프로미스의 상태

| 상태(promise state) | 의미             | 상태 변경 조건           |
| ----------------- | -------------- | ------------------ |
| pending           | 비동기 처리 중       | new Promise() 실행 시 |
| fulfilled         | 비동기 처리 성공(이행됨) | resolve 함수 호출 시    |
| rejected          | 비동기 처리 실패(거부됨) | reject 함수 호출 시     |

* 프로미스는 pending → fulfilled/rejected 상태로 단방향으로만 진행됨.

#### 상태 전이 그림

* **pending → fulfilled**: `resolve(value)`
* **pending → rejected**: `reject(error)`

```js
const fulfilled = new Promise(resolve => resolve());
```

---
이어지는 부분을 **마크다운**으로 아래와 같이 구성해줬어.
**예제 코드, 표, 주요 설명 모두 최대한 원문에 가깝게 넣었고**, 너가 필요한 실사용 코드 활용에 중점을 뒀어.
(섹션 번호, 코드, 표, 구조 모두 네가 요청한 대로 상세하게 정리.)

---

## 45.3 프로미스의 후속 처리 메서드

비동기 처리 결과(fulfilled/rejected)에 따라 후속 처리를 하는 메서드는 `.then`, `.catch`, `.finally`가 있다.

---

### 45.3.1 Promise.prototype.then

* `then` 메서드는 두 개의 콜백 함수(성공/실패)를 인수로 전달받음.
* 반환값은 항상 새로운 프로미스.
* **fulfilled**: 첫 번째 콜백 호출.
* **rejected**: 두 번째 콜백 호출.

```js
// fulfilled
new Promise(resolve => resolve('fulfilled'))
  .then(v => console.log(v), e => console.error(e)); // fulfilled

// rejected
new Promise((_, reject) => reject(new Error('rejected')))
  .then(v => console.log(v), e => console.error(e)); // Error: rejected
```

---

### 45.3.2 Promise.prototype.catch

* `catch` 메서드는 `.then(undefined, onRejected)`와 동일하게 동작.
* 주로 체이닝 마지막에서 에러를 처리할 때 사용.

```js
// rejected
new Promise((_, reject) => reject(new Error('rejected')))
  .catch(e => console.log(e)); // Error: rejected
```

```js
// then의 두 번째 인수 생략
new Promise((_, reject) => reject(new Error('rejected')))
  .then(undefined, e => console.log(e)); // Error: rejected
```

---

### 45.3.3 Promise.prototype.finally

* `.finally` 메서드는 fulfilled/rejected 상관없이 무조건 마지막에 실행됨.

```js
new Promise(() => {})
  .finally(() => console.log('finally')); // finally
```

* 주로 리소스 정리, 후처리 등에 활용.

---

## 45.4 프로미스의 에러 처리

* 프로미스 후속 처리 메서드인 `.then`, `.catch`, `.finally`로 비동기 처리 중 발생한 에러를 효과적으로 처리 가능.

```js
const promiseGet = url => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();
    xhr.onload = () => {
      if (xhr.status === 200) {
        resolve(JSON.parse(xhr.response));
      } else {
        reject(new Error(xhr.status));
      }
    };
  });
};

const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1';

promiseGet(wrongUrl)
  .then(res => console.log(res))
  .catch(err => console.error(err)); // Error: 404
```

---

## 45.5 프로미스 체이닝

* `.then`, `.catch`, `.finally`를 연속적으로 이어붙여(체이닝) 복잡한 비동기 흐름을 단순하게 만들 수 있음.
* 체이닝을 활용하면 콜백 지옥 없이 순차적, 병렬 비동기 작업을 효과적으로 관리할 수 있다.

```js
const url = 'https://jsonplaceholder.typicode.com';

promiseGet(`${url}/posts/1`)
  .then(userId => promiseGet(`${url}/users/${userId}`))
  .then(userInfo => console.log(userInfo))
  .catch(err => console.error(err));
```

* **then, catch, finally** 후속 처리 메서드는 항상 새로운 프로미스를 반환.

---

### 표: 후속 처리 메서드와 반환값

| 후속 처리 메서드 | 콜백 함수의 인자               | 후속 처리 메서드 반환값 |
| --------- | ----------------------- | ------------- |
| then      | Promise가 반환한 값/resolve값 | 새로운 프로미스      |
| catch     | Error 객체                | 새로운 프로미스      |
| finally   | 없음                      | 새로운 프로미스      |

---

## 45.6 프로미스의 정적 메서드

Promise 객체에는 생성자 함수로부터 직접 호출할 수 있는 정적 메서드가 있음.

---

### 45.6.1 Promise.resolve / Promise.reject

* **Promise.resolve(value)**: value를 fulfilled 상태로 하는 프로미스를 반환.
* **Promise.reject(error)**: error를 rejected 상태로 하는 프로미스를 반환.

```js
const resolvedPromise = Promise.resolve([1, 2, 3]);
resolvedPromise.then(console.log); // [1, 2, 3]
```

---

### 45.6.2 Promise.all

* 여러 개의 비동기 처리를 **병렬**로 처리.
* 모든 프로미스가 fulfilled될 때까지 대기 후, 결과를 배열로 반환.
* 하나라도 rejected면 즉시 종료하고 에러 반환.

```js
const requestData1 = () =>
  new Promise(resolve => setTimeout(() => resolve(1), 3000));
const requestData2 = () =>
  new Promise(resolve => setTimeout(() => resolve(2), 2000));
const requestData3 = () =>
  new Promise(resolve => setTimeout(() => resolve(3), 1000));

Promise.all([requestData1(), requestData2(), requestData3()])
  .then(console.log) // [1, 2, 3] (모두 완료될 때까지 대기)
  .catch(console.error);
```

* **하나라도 rejected라면**:

```js
Promise.all([
  new Promise((_, reject) => setTimeout(() => reject(new Error('Error 1')), 3000)),
  new Promise((_, reject) => setTimeout(() => reject(new Error('Error 2')), 2000)),
  new Promise((_, reject) => setTimeout(() => reject(new Error('Error 3')), 1000)),
])
.then(console.log)
.catch(console.log); // Error: Error 3 (가장 먼저 에러난 것만 반환)
```

---

### 45.6.3 Promise.race

* 여러 개의 프로미스 중 **가장 먼저 fulfilled/rejected**된 프로미스의 결과만 반환.
* 먼저 끝나는 것의 결과만 받음.

```js
Promise.race([
  new Promise(resolve => setTimeout(() => resolve(1), 1000)),
  new Promise(resolve => setTimeout(() => resolve(2), 2000)),
  new Promise(resolve => setTimeout(() => resolve(3), 3000)),
])
.then(console.log) // 1
.catch(console.error);
```

* 만약 에러가 더 빨리 발생하면 그 에러 반환:

```js
Promise.race([
  new Promise((_, reject) => setTimeout(() => reject(new Error('Error 1')), 1000)),
  new Promise((_, reject) => setTimeout(() => reject(new Error('Error 2')), 2000)),
  new Promise((_, reject) => setTimeout(() => reject(new Error('Error 3')), 3000)),
])
.then(console.log)
.catch(console.log); // Error: Error 1
```

---

### 45.6.4 Promise.allSettled

* 모든 프로미스가 **완료될 때까지**(fulfilled든 rejected든) 기다렸다가 각 결과의 상태와 값을 배열로 반환.

```js
Promise.allSettled([
  new Promise(resolve => setTimeout(() => resolve(1), 2000)),
  new Promise((_, reject) => setTimeout(() => reject(new Error('Error!')), 1000)),
])
.then(console.log);
// [
//   { status: 'fulfilled', value: 1 },
//   { status: 'rejected', reason: Error: ... }
// ]
```

---

## 45.7 마이크로태스크 큐

* 프로미스의 후속 처리 메서드는 **마이크로태스크 큐**에서 실행됨.
* setTimeout보다 **우선순위가 높음**.

```js
setTimeout(() => console.log(1), 0);
Promise.resolve().then(() => console.log(2));
console.log(3);
// 출력: 3, 2, 1
```

---

## 45.8 fetch

* **fetch** 함수는 XMLHttpRequest를 대체하는 최신 HTTP 요청 API.
* fetch 함수는 항상 프로미스를 반환, `Response` 객체를 resolve함.
* 실제 네트워크 에러가 아닌 한 HTTP 에러(404, 500)는 reject가 아니라 resolve로 반환됨.

### fetch 기본 사용법

```js
fetch('https://jsonplaceholder.typicode.com/todos/1')
  .then(response => response.json())
  .then(json => console.log(json));
// {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
```

### 에러 핸들링

```js
const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1';

fetch(wrongUrl)
  .then(response => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .catch(console.error);
```

---

### fetch를 이용한 HTTP 메서드 래핑 예시

```js
const request = {
  get(url) {
    return fetch(url);
  },
  post(url, payload) {
    return fetch(url, {
      method: 'POST',
      headers: { 'content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });
  },
  patch(url, payload) {
    return fetch(url, {
      method: 'PATCH',
      headers: { 'content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });
  },
  delete(url) {
    return fetch(url, { method: 'DELETE' });
  }
};
```

---

### 각 HTTP 메서드별 사용 예시

#### 1. GET 요청

```js
request.get('https://jsonplaceholder.typicode.com/todos/1')
  .then(response => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then(todo => console.log(todo))
  .catch(err => console.error(err));
// {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
```

#### 2. POST 요청

```js
request.post('https://jsonplaceholder.typicode.com/todos', {
  userId: 1,
  title: 'JavaScript',
  completed: false
})
.then(response => {
  if (!response.ok) throw new Error(response.statusText);
  return response.json();
})
.then(todo => console.log(todo))
.catch(err => console.error(err));
// {userId: 1, title: "JavaScript", completed: false, ...}
```

#### 3. PATCH 요청

```js
request.patch('https://jsonplaceholder.typicode.com/todos/1', {
  completed: true
})
.then(response => {
  if (!response.ok) throw new Error(response.statusText);
  return response.json();
})
.then(todo => console.log(todo))
.catch(err => console.error(err));
// {userId: 1, id: 1, title: '...', completed: true}
```

#### 4. DELETE 요청

```js
request.delete('https://jsonplaceholder.typicode.com/todos/1')
  .then(response => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then(todo => console.log(todo))
  .catch(err => console.error(err));
```

---

### 참고: fetch의 프로미스 동작 방식 요약

* HTTP 에러(404 등)는 **reject**가 아니라 **resolve**됨. (response.ok로 수동 체크 필요)
* 네트워크 에러(인터넷 연결 실패 등)만 **reject**됨.
* Response 객체는 다양한 HTTP 정보를 가지고 있음.

---

## 45.9 종합 예제/정리

### Promise.all, map, fetch 등 실전 예시

```js
const githubIds = ['jeresig', 'ahejlsberg', 'ungmo2'];

Promise.all(githubIds.map(id =>
  promiseGet(`https://api.github.com/users/${id}`)
))
  .then(users => users.map(user => user.name))
  .then(console.log); // ['John Resig', 'Anders Hejlsberg', 'Ungmo Lee']
```

---

## 핵심 요약

* **콜백 지옥**을 해결하기 위한 구조적 비동기 처리 방식이 프로미스.
* `.then`, `.catch`, `.finally` 체이닝으로 복잡한 로직도 읽기 쉽게 처리.
* `Promise.all`, `Promise.race`, `Promise.allSettled` 등 여러 프로미스 처리 메서드로 병렬/최적/전체 결과 관리.
* **fetch**는 최신 비동기 HTTP 요청 표준, 직접적으로 프로미스 기반으로 사용됨.

