## 29 Math

표준 빌트인 객체인 Math는 수학적인 상수와 함수를 위한 프로퍼티와 메서드를 제공.
Math는 생성자 함수가 아니므로 인스턴스를 생성할 수 없음. 오직 정적 프로퍼티와 메서드만 제공.

## 29.1 Math 프로퍼티

### 29.1.1 Math.PI

- 원주율 \(\pi \) 값을 반환.

```js
Math.PI; // 3.141592653589793
```

## 29.2 Math 메서드

### 29.2.1 Math.abs

- 인수로 전달된 숫자의 절대값을 반환.
- 절대값은 반드시 0 또는 양수.

```js
Math.abs(-1);    // 1
Math.abs('-1');  // 1
Math.abs('');    // 0
Math.abs([]);    // 0
Math.abs(null);  // 0
Math.abs(undefined); // NaN
Math.abs({});    // NaN
Math.abs('string'); // NaN
Math.abs();      // NaN
```

### 29.2.2 Math.round

- 인수로 전달된 숫자의 소수점 이하를 반올림한 정수를 반환.

```js
Math.round(1.4);    // 1
Math.round(1.6);    // 2
Math.round(-1.4);   // -1
Math.round(-1.6);   // -2
Math.round();       // NaN
```

### 29.2.3 Math.ceil

- 인수로 전달된 숫자의 소수점 이하를 올림한 정수를 반환.

```js
Math.ceil(1.4);    // 2
Math.ceil(1.6);    // 2
Math.ceil(-1.4);   // -1
Math.ceil(-1.6);   // -1
Math.ceil();       // NaN
```

### 29.2.4 Math.floor

- 인수로 전달된 숫자의 소수점 이하를 내림한 정수를 반환.

```js
Math.floor(1.9);    // 1
Math.floor(0.9);    // 0
Math.floor(-1.9);   // -2
Math.floor(-0.9);   // -1
Math.floor(1);      // 1
Math.floor();       // NaN
```

### 29.2.5 Math.sqrt

- 인수로 전달된 숫자의 제곱근을 반환.

```js
Math.sqrt(9);    // 3
Math.sqrt(-9);   // NaN
Math.sqrt(2);    // 1.414213562373095
Math.sqrt(1);    // 1
Math.sqrt();     // NaN
```

### 29.2.6 Math.random

- 0 이상 1 미만의 난수(랜덤 숫자)를 반환.

```js
Math.random(); // 예: 0.8208720231391746
```

- 예제: 1~10 범위의 정수 구하기

```js
const random = Math.floor((Math.random() * 10) + 1);
console.log(random); // 1~10 범위의 정수
```

### 29.2.7 Math.pow

- 첫 번째 인수를 밑(base)으로, 두 번째 인수를 지수(exponent)로 거듭제곱한 결과를 반환.

```js
Math.pow(2, 8);   // 256
Math.pow(2, -1);  // 0.5
Math.pow(2);      // NaN
```

- ES7에서는 지수 연산자(`**`)를 사용 가능.

```js
2 ** 2 ** 2;      // 16
Math.pow(Math.pow(2, 2), 2); // 16
```

### 29.2.8 Math.max

- 전달받은 인수 중 가장 큰 수를 반환. 인수가 없으면 -Infinity 반환.

```js
Math.max(1);         // 1
Math.max(1, 2);      // 2
Math.max(1, 2, 3);   // 3
Math.max();          // -Infinity
```

- 배열 요소 중 최대값 구하기

```js
Math.max.apply(null, [1, 2, 3]); // 3
Math.max(...[1, 2, 3]);          // 3 (ES6 스프레드 문법)
```

### 29.2.9 Math.min

- 전달받은 인수 중 가장 작은 수를 반환. 인수가 없으면 Infinity 반환.

```js
Math.min(1);         // 1
Math.min(1, 2);      // 1
Math.min(1, 2, 3);   // 1
Math.min();          // Infinity
```

- 배열 요소 중 최소값 구하기

```js
Math.min.apply(null, [1, 2, 3]); // 1
Math.min(...[1, 2, 3]);          // 1 (ES6 스프레드 문법)
```

