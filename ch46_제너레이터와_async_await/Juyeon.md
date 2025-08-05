# 46. 제너레이터와 async/await

## 46.1 제너레이터란?

제너레이터는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수다.

<제너레이터와 일반 함수의 차이>

1. 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다.
2. 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다.(양방향)
3. 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.

## 46.2 제너레이터 함수의 정의

제너레이터 함수는 function* 키워드로 선언한다. 그리고 하나 이상의 yield 표현식을 포함한다.

![image.png](attachment:38025358-459c-45b7-a499-3432ba4f262c:81504c0a-c272-49bc-b632-79c4e5c58095.png)

![image.png](attachment:2ab4591e-4122-47ec-8ca3-f8d3931121ce:image.png)

제너레이터 함수는 화살표 함수로 정의할 수 없다.

제너레이터 함수는 new 연산자와 함께 생성자 함수로 호출할 수 없다.

## 46.3 제너레이터 객체

제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성
해 반환한다. 제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.

??? 이터레이터 ?

iterator 정의 : {value : 값 , done : true/false}형태의 이터레이터 객체를 리턴하는 next() 메서드를 가진 객체. next 메서드로 순환 할 수 있는 객체다.

제너레이터 객체는 next 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 return, throw 메서드를 갖는다.

return 메서드를 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.

throw 메서드를 호출하면 인수로 전달받은 에러를 발생시키고 undefined를 value 프루퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.

## 46.4 제너레이터의 일시 중지와 재개

- 제너레이터 객체의 next 메서드를 호출하면 yield 표현식까지 실행되고 일시 중지된다. 이때 함수의 제어권이 호출자로 양도된다.
- 이후 필요한 시점에 호출자가 또다시 next 메서드를 호출하면 일시 중지된 코드부터 실행을 재개하기 시작하여 다음 yield 표현식까지 실행되고 또 다시 일시 중지된다.
- 제너레이터 객체의 next 메서드는 value, done 프로퍼티를 갖는 이터레이터 리절트 객체({value, done})를 반환한다.
- next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 yield 표현식에서 yield된 값(yield키워드 뒤의 값)이 할당되고 done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언값이 할당된다.

![image.png](attachment:cd34e633-ee9c-4fe3-9589-376deff6cac3:image.png)

![image.png](attachment:94182148-ba4f-49a6-8866-6d83f67d7019:image.png)

- 제너레이터 객체의 next 메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당된다.

```jsx
function* genFunc(){
	const x = yield 1; 
	const y = yield (x + 10);
	return x + y
}

const generator = genFunc(0);

let res = generator.next(); 
// yield 1 표현식까지 실행되고 일시 중지. 값이 value 프로퍼티에 할당됨. x변수에는 아직 할당 안됨.
console.log(res); // {value: 1, done: false};

generator.next(10); 
// 인수 10은 첫 번째 yield 표현식을 할당받는 x변수에 할당됨. yield(x+10) 표현식까지 실행되고 일시 중지. x+10의 값 20이 value 프로퍼티에 할당됨.
console.log(res); // {value: 20, done: false};

generator.next(20);
// 인수 20은 두 번째 yield 표현식을 할당받는 y변수에 할당됨. 함수 반환값 x+y는 value 프로퍼티에 할당됨. 
console.log(res); // {value: 30, done: true};

// 일반적으로 제너레이터의 반환값은 의미가 없다.
// 따서 제너레이터의 값을 반환할 필요가 없고 return은 종료의 의미로만 사용해야 한다.
```

## 46.5 제너레이터의 활용

### 46.5.1 이터러블의 구현

- 제너레이터 함수를 사용하면 이터레이션 프로토콜을 준수해 이터러블을 생성하는 방식보다 간단히 이터러블을 구현할 수 있다.

### 46.5.2 비동기 처리

- 제너레이터 함수는 next 메서드와 yield 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있다. 이러한 특성을 활용하면 프로미스를 사용한 비동기 처리를 동기 처리처럼 구현할 수 있다.
- 다시 말해, 프로미스의 후속 처리 메서드 then/catch/finally 없이 비동기 처리 결과를 반환하도록 구현할 수 있다.

⇒ async/await를 사용하면 async 함수와 같은 제너레이터 실행기를 사용할 필요가 없지만 혹시 제너레이터 실행기가 필요하다면 직접 구현하는 것보다 CO 라이브러리 3를 사용하기 바란다.

## 46.6 async/await

async/await는 프로미스를 기반으로 동작한다. 

async/await를 사용하면 프로미스의 then/catch/finally 후속 처리 메서드에 콜백 함수를 전달해서 비동기 처리 걸과를 후속 처리할 필요 없이 마치 동기 처리처럼 프로미스를 사용할 수 있다.

![image.png](attachment:437192d3-c938-40a2-b367-fdafb77c7e94:image.png)

![image.png](attachment:8beeb133-0779-421f-a789-35a4bd1ec0a4:image.png)

### 46.6.1 async함수

await 키워드는 반드시 async 함수 내부에서 사용해야 한다.

async 함수는 async 키워드를 사용해 정의하며 언제나 프로미스를 반환한다.

async 함수가 명시적으로 프로미스를 반환하지 않더라도 async 함수는 암묵적으로 반환값을 resolve하는 프로미스를 반환한다.

![image.png](attachment:7c290c2a-ef5a-48da-8734-73bec54c8444:image.png)

### 46.6.2 await 키워드

await 키워드는 프로미스가 settled 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 settled 상
태가 되면 프로미스가 resolve한 처리 결과를 반환한다. await 키워드는 반드시 프로미스 앞에서 사용해야 한다.

![image.png](attachment:9605b21f-14f3-4eb0-9e77-d4bf862bb19d:image.png)

1의 fetch 함수가 수행한 HTTP 요청에 대한 서버의 응답이 도착해서 fetch 함수가 반환한 프로미스가 settled 상태가 될 때까지 1은 대기하게 된다. 이후 프로미스가 settled 상태가 되면 프로미스가 resolve한 처리 결과가 res 변수에 할당된다.

### 46.6.3 에러 처리

async/await에서 에러 처리는 try... catch 문을 사용할 수 있다. 콜백 함수를 인수로 전달받는 비동기 함수
와는 달리 프로미스를 반환하는 비동기 함수는 명시적으로 호출할 수 있기 때문에 호출자가 명확하다.

![image.png](attachment:59edf79d-7085-4c4a-9b43-ee8070ac758e:image.png)

try 코드 블록 내의 모든 문에서 발생한 일반적인 에러까지 모두 캐치할 수 있다.

async 함수 내에서 catch 문을 사용해서 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스를 반환한다. 따라서 async 함수를 호출하고 Promise. prototype.catch 후속 처리 메서드를 사용해 에러를 캐치할 수도 있다.

```jsx
foo()
.then(console.log)
.catch(console.error); // TypeError: Failed to fetch
```
