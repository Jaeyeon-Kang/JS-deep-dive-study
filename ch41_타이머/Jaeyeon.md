# 41장 타이머

## 41.1 호출 스케줄링

자바스크립트 엔진은 싱글 스레드로 동작한다. 따라서 함수를 명시적으로 호출하지 않고 일정 시간이 경과된 이후에 호출되도록 예약하려면 **타이머 함수**를 사용해야 한다. 이를 \*\*호출 스케줄링(call scheduling)\*\*이라고 한다. 대표적인 타이머 함수는 `setTimeout`, `setInterval`이며, 각각 단일 호출과 반복 호출을 예약할 수 있다. 이들 함수는 **비동기(asynchronous)** 처리 방식으로 동작한다.

* `setTimeout`: 일정 시간이 지난 후 단 한 번 콜백 함수 실행
* `setInterval`: 일정 시간 간격마다 콜백 함수 반복 실행
* `clearTimeout`, `clearInterval`: 각각 타이머 취소

---

## 41.2 타이머 함수

`setTimeout` 함수와 `setInterval` 함수는 함수 호출을 일정 시간 이후에 예약하는 호출 스케줄링(call scheduling) 기능을 제공한다.

### 41.2.1 `setTimeout` / `clearTimeout`

`setTimeout(func, delay, param1, param2, ...);`

* **func**: 타이머가 만료되었을 때 호출될 콜백 함수 또는 문자열 코드
* **delay**: 지연 시간(단위: ms). 생략 시 기본값 `0`이 지정된다. `delay`가 `4ms` 이하인 경우 최소 지연 시간 `4ms`가 적용된다.
* **param1, param2, ...**: 콜백 함수에 전달될 추가 인수들 (구 버전 브라우저에서는 미지원)

> 딜레이 시간이 설정된 타이머가 만료되면, 콜백 함수가 즉시 호출되는 것이 보장되지 않는다. 지연 시간은 **태스크 큐**에 콜백 함수를 등록하는 시간을 지연할 뿐이다.

#### 예제 41-01

```js
// 1초(1000ms) 후 타이머 만료되어 콜백 함수 호출
setTimeout(() => console.log('Hi!'), 1000);

// 이름 인수를 전달하여 콜백에서 사용
setTimeout(name => console.log(`Hi! ${name}`), 1000, 'Lee');
```

#### 예제 41-02

```js
// 타이머 ID로 등록 취소 가능
const timerId = setTimeout(() => console.log('Hi!'), 1000);
clearTimeout(timerId); // 타이머 취소
```

### 41.2.2 `setInterval` / `clearInterval`

`setInterval(func, delay, param1, param2, ...);`

* `setInterval` 함수는 두 번째 인수로 전달받은 시간(`delay`)으로 **반복 동작**하는 타이머를 생성한다.
* 첫 번째 인수(`func`)로 전달받은 콜백 함수가 지정된 간격마다 호출된다.
* **delay**: 반복 간격(단위: ms). 생략 시 기본값 `0`이 지정되며, `4ms` 이하인 경우 최소 지연 시간 `4ms`가 적용된다.
* **func**: 타이머가 만료될 때마다 호출될 콜백 함수 또는 문자열 코드
* **param1, param2, ...**: 콜백 함수에 전달될 추가 인수들

> `setInterval`은 지정된 간격마다 **태스크 큐**에 콜백을 등록하며, 콜 스택이 비어 있을 때마다 이벤트 루프를 통해 실행된다.

#### 예제 41-03

```js
let count = 1;

// 1초(1000ms) 간격으로 count를 출력
const timerId = setInterval(() => {
  console.log(count); // 1 2 3 4 5
  if (count++ === 5) {
    clearInterval(timerId); // count가 5가 되면 반복 중지
  }
}, 1000);

// 추가 인수 전달 예제
const timerId2 = setInterval(name => console.log(`Hi! ${name}`), 1000, 'Lee');

// 반복 중지
clearInterval(timerId2);
```

---

## 41.3 디바운스와 스로틀

브라우저에서 `scroll`, `resize`, `input` 등의 이벤트는 짧은 시간 간격으로 연속해서 발생한다. 이러한 이벤트에 바인딩된 이벤트 핸들러가 과도하게 호출되면 성능 문제가 발생할 수 있다. \*\*디바운스(debounce)\*\*와 **스로틀(throttle)** 기법은 짧은 시간에 연속 발생하는 이벤트를 그룹화하여 과도한 이벤트 핸들러 호출을 방지하는 프로그래밍 기법이다.

### 41.3.1 디바운스

* 마지막 이벤트가 발생한 후 일정 시간(`delay` ms) 동안 추가 이벤트가 없을 때 콜백을 실행한다.

#### 예제 41-05

```html
<!DOCTYPE html>
<html>
<body>
  <input type="text">
  <div class="msg"></div>
  <script>
    const $input = document.querySelector('input');
    const $msg = document.querySelector('.msg');

    /**
     * debounce 함수는 타이머 ID를 기억하는 클로저를 반환한다.
     * delay가 경과하기 이전에 이벤트가 발생하면 이전 타이머를 취소하고 새로운 타이머를 재설정한다.
     */
    const debounce = (callback, delay) => {
      let timerId;
      return event => {
        if (timerId) clearTimeout(timerId);
        timerId = setTimeout(callback, delay, event);
      };
    };

    // debounce 함수가 반환하는 클로저를 input 이벤트 핸들러로 등록
    // 300ms 동안 추가 input 이벤트가 없으면 한 번만 실행
    $input.oninput = debounce(e => {
      $msg.textContent = e.target.value;
    }, 300);
  </script>
</body>
</html>
```

### 41.3.2 스로틀

* 일정 시간(`delay` ms) 동안 첫 번째 이벤트가 발생하면 콜백을 실행하고, 그 이후 주기 동안 추가 호출을 무시한다.

#### 예제 41-06

```html
<!DOCTYPE html>
<html>
<head>
  <style>
    .container {
      width: 300px;
      height: 300px;
      overflow: scroll;
      background-color: rebeccapurple;
    }
    .content {
      width: 300px;
      height: 1000vh;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="content"></div>
  </div>
  <p>일반 이벤트 핸들러가 scroll 이벤트 처리할 횟수: <span class="normal-count">0</span></p>
  <p>스로틀 이벤트 핸들러가 scroll 이벤트 처리할 횟수: <span class="throttle-count">0</span></p>

  <script>
    const $container = document.querySelector('.container');
    const $normalCount = document.querySelector('.normal-count');
    const $throttleCount = document.querySelector('.throttle-count');

    // 일반 핸들러
    let normalCount = 0;
    $container.addEventListener('scroll', () => {
      $normalCount.textContent = ++normalCount;
    });

    // throttle 구현
    const throttle = (callback, delay) => {
      let timerId;
      return event => {
        if (timerId) return;
        timerId = setTimeout(() => {
          callback(event);
          timerId = null;
        }, delay);
      };
    };

    // throttle 핸들러 등록
    let throttleCount = 0;
    $container.addEventListener('scroll', throttle(() => {
      $throttleCount.textContent = ++throttleCount;
    }, 100));
  </script>
</body>
</html>
```
