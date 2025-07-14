# 42장 비동기 프로그래밍

## 42.1 동기 처리와 비동기 처리

자바스크립트 엔진은 단 하나의 실행 컨텍스트 스택을 가지며, 현재 실행 중인 실행 컨텍스트(스택 최상위 요소)만 실행되고 나머지는 모두 대기 상태이다. 즉, 두 가지 이상의 태스크를 동시에 실행할 수 없으며, 이것을 **싱글 스레드(single thread)** 방식이라고 한다. 이로 인해 무거운 연산이나 블로킹 함수가 실행되는 동안 다른 코드와 이벤트 핸들러는 모두 기다려야 한다.

동기 처리는 태스크를 순차적으로 하나씩 처리하므로 실행 순서가 보장되지만, 앞선 태스크가 완료될 때까지 이후 태스크들이 모두 블로킹(blocking)되는 단점이 있다.

반면, **비동기(asynchronous)** 처리 방식은 일부 태스크를 블로킹하지 않고 곧바로 다음 태스크를 실행한다. 이를 위해 전통적으로 \*\*콜백 패턴(callback pattern)\*\*을 사용하며, 비동기 함수는 내부에서 콜백 함수를 호출하도록 설계된다.

자바스크립트에서 대표적인 비동기 처리 함수는 다음과 같다:

* **타이머 API**: `setTimeout`, `setInterval`, `clearTimeout`, `clearInterval`
* **HTTP 요청**: `fetch`, `XMLHttpRequest`
* **이벤트 핸들러**: `addEventListener` 등이 비동기 방식으로 동작

### 예제 42-01 (동기 함수)

```js
const foo = () => {};
const bar = () => {};

foo();
bar();
```

* 실행 컨텍스트 스택에 순서대로 `foo`와 `bar`가 푸시되고, 이전 함수가 완료된 후에야 다음 함수가 실행된다.

### 예제 42-02 (동기적 sleep 구현)

```js
// sleep 함수는 지정된 delay(ms) 후에 func를 호출하지만 그 사이에 블로킹을 발생시킨다.
function sleep(func, delay) {
  const delayUntil = Date.now() + delay;
  while (Date.now() < delayUntil);
  func();
}

function foo() {
  console.log('foo');
}

function bar() {
  console.log('bar');
}

// sleep 호출: 약 3초간 블로킹 후 foo 실행
sleep(foo, 3 * 1000);
bar();
// 출력 순서: 3초 후 'foo' → 즉시 'bar'
```

* 위 예제에서 `sleep`은 콜스택을 비우지 않고 CPU를 점유(blocking)하므로, 이후 `bar`는 sleep이 끝난 후에 실행된다.

### 예제 42-03 (비동기적 setTimeout)

```js
function foo() { console.log('foo'); }
function bar() { console.log('bar'); }

// setTimeout은 콜백을 태스크 큐에 등록하고 즉시 반환한다.
setTimeout(foo, 0);
bar();
// 출력 순서:
// bar
// foo
```

* `setTimeout`은 동기 함수가 아니며, 내부적으로 브라우저(또는 Node.js)가 제공하는 타이머 API를 사용해 콜백을 **태스크 큐**에 등록한다.
* 현재 콜 스택이 비워진 후 \*\*이벤트 루프(Event Loop)\*\*가 태스크 큐에서 콜백을 dequeue하여 실행하므로 비동기 처리가 가능하다.

## 42.2 이벤트 루프와 태스크 큐

> **JavaScript 엔진은 싱글 스레드로 동작하지만, 브라우저는 멀티스레드로 동작한다.**
>
> 여기서 싱글 스레드로 동작하는 것은 브라우저 자체가 아닌, 브라우저에 내장된 JavaScript 엔진이라는 점에 주의해야 한다. 브라우저는 JavaScript 엔진 외에도 렌더링 엔진(Rendering Engine)과 다양한 Web API를 멀티스레드로 제공한다.
>
> * **JavaScript 엔진**: JS 코드 파싱, 실행을 담당하는 싱글 스레드 환경
> * **렌더링 엔진**: HTML, CSS 파싱 및 레이아웃, 그리기 등 UI 렌더링을 담당
> * **Web API**: `setTimeout`, `XMLHttpRequest`, DOM 이벤트 등 비동기 API를 백그라운드 스레드에서 처리하고 콜백을 태스크 큐에 등록

### 예제 42-04

```js
console.log('start');
setTimeout(() => console.log('timeout'), 0);
console.log('end');
```

위 코드를 실행하면 다음과 같은 순서로 출력된다:

```
start
end
timeout
```

이는 `setTimeout`이 비동기 함수이기 때문이다. 비동기 함수가 호출되면 해당 콜백은 즉시 실행되지 않고, \*\*태스크 큐(Task Queue)\*\*에 푸시되어 대기한다. 이후 \*\*이벤트 루프(Event Loop)\*\*가 콜 스택(Call Stack)을 확인하여 스택이 비어 있으면 태스크 큐에서 대기 중인 콜백을 스택에 푸시해 실행한다.

#### 실행 순서

1. 전역 코드가 평가되어 전역 실행 컨텍스트(Global Execution Context)가 생성된다.
2. 전역 실행 컨텍스트가 콜 스택(Call Stack)에 푸시된다.
3. `console.log('start')`가 즉시 실행되어 `start` 출력.
4. `setTimeout` 호출: 비동기 타이머 API에게 콜백을 등록하고 바로 반환.
5. `console.log('end')`가 즉시 실행되어 `end` 출력.
6. 전역 코드가 모두 실행되어 전역 실행 컨텍스트가 팝(popped)되고 콜 스택이 비어짐.
7. 이벤트 루프가 콜 스택이 비어 있음을 감지.
8. 태스크 큐에 대기 중인 `setTimeout` 콜백(`() => console.log('timeout')`)을 콜 스택에 푸시.
9. 콜백이 실행되어 `timeout` 출력.
10. 콜백 실행 완료 후 콜 스택이 다시 비게 됨.

이처럼 **비동기 함수**(`setTimeout`, `setInterval`, HTTP 요청, 이벤트 핸들러 등)는 전통적인 **콜백 패턴**을 사용해, 실행 컨텍스트를 블로킹하지 않고 태스크 큐에 콜백을 등록한 뒤, 이벤트 루프를 통해 처리한다.
