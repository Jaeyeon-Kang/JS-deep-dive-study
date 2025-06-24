## 40.1 이벤트 드리븐 프로그래밍

* 브라우저는 처리해야 할 특정 사건(ex. 클릭, 키보드 입력, 마우스 이동)이 발생하면 이를 감지하여 이벤트를 발생시킨다.
* 만약 애플리케이션이 특정 타입의 이벤트에 대해 반응하여 어떤 일을 하고 싶다면 해당하는 타입의 이벤트가 발생했을 때 호출될 함수를 브라우저에게 알려 호출을 위임한다.
* 이때 이벤트가 발생했을 때 호출될 함수를 이벤트 핸들러라 하고, 이벤트가 발생했을 때 브라우저에게 이벤트 핸들러의 호출을 위임하는 것을 이벤트 핸들러 등록이라 한다.
* 이처럼 이벤트와 그에 대응하는 함수(이벤트 핸들러)를 통해 사용자와 애플리케이션은 상호작용을 할 수 있다.
* 이와 같이 프로그램의 흐름을 이벤트 중심으로 제어하는 프로그래밍을 이벤트 드리븐 프로그래밍이라 한다.

## 40.2 이벤트 타입

* 이벤트 타입은 이벤트의 종류를 나타내는 문자열이다.
* ex. 'click' : 사용자가 마우스 버튼을 클릭했을 때 발생하는 이벤트
* 이벤트 타입은 약 200여 가지가 있다.
* 이벤트 타입 상세 목록은 MDN의 Event reference에서 확인할 수 있다.

### 40.2.1 마우스 이벤트
![image](https://github.com/user-attachments/assets/08d183a9-d879-444f-81f1-24f4f362644d)

![image](https://github.com/user-attachments/assets/af6a2000-0438-45d8-9792-3fc5f36ed92e)

### 40.2.2 키보드 이벤트
![image](https://github.com/user-attachments/assets/ce115d9e-113f-40fd-aafd-a77712996e3d)


### 40.2.3 포커스 이벤트

![image](https://github.com/user-attachments/assets/c15305a5-b7cd-4e5d-9c91-93debd2dbf00)


* focusin, focusout 이벤트를 이벤트 핸들러 프로퍼티 방식으로 등록하면 크롬, 사파리에서 정상 동작하지 않으므로 addEventListener 메서드 방식을 사용해 등록해야 한다.

### 40.2.4 폼 이벤트
![image](https://github.com/user-attachments/assets/feb00bbf-dc91-4fc5-b93e-10d7026d9c1c)


### 40.2.5 값 변경 이벤트
![image](https://github.com/user-attachments/assets/5c11b34a-9b3d-430d-b421-b64e51340244)

### 40.2.6 DOM 뮤테이션 이벤트

![image](https://github.com/user-attachments/assets/6a4956f7-c72b-4e5b-a65c-810a6f514ee4)


### 40.2.7 뷰 이벤트
![image](https://github.com/user-attachments/assets/da1f3c2c-005c-4498-b40b-0e3960245158)

### 40.2.8 리소스 이벤트

![image](https://github.com/user-attachments/assets/017ca32c-5eed-4fb1-aad8-09d5d32d9995)

## 40.3 이벤트 핸들러 등록

* 이벤트가 발생하면 브라우저에 의해 호출될 함수가 이벤트 핸들러다.
* 이벤트가 발생했을 때 브라우저에게 이벤트 핸들러의 호출을 위임하는 것을 이벤트 핸들러 등록이라 한다.
* 이벤트 핸들러를 등록하는 방법은 3가지다.

### 40.3.1 이벤트 핸들러 어트리뷰트 방식

* HTML 요소의 어트리뷰트 중에는 이벤트에 대응하는 이벤트 핸들러 어트리뷰트가 있다.
* 이벤트 핸들러 어트리뷰트의 이름은 onclick과 같이 on 접두사와 이벤트의 종류를 나타내는 이벤트 타입으로 이루어져 있다.
* 이벤트 핸들러 어트리뷰트 값으로 함수 호출문 등의 문을 할당하면 이벤트 핸들러가 등록된다.
* 주의 : 이벤트 핸들러 어트리뷰트 값으로 함수가 아닌 값을 반환하는 함수 호출문을 이벤트 핸들러로 등록하면 브라우저가 이벤트 핸들러를 호출할 수 없다. 함수 참조를 등록해야 브라우저가 이벤트 핸들러를 호출할 수 있다.

```js
<!DOCTYPE html>
<html>
<body>
  <button onclick="sayHi('Lee')">Click me!</button>
  <script>
    function sayHi(name){
      console.log(`Hi! ${name}.`);
    }
  </script>
</body>
</html>
```

### 40.3.2 이벤트 핸들러 프로퍼티 방식

* window 객체와 Document, HTMLElement 타입의 DOM 노드 객체는 이벤트에 대응하는 이벤트 핸들러 프로퍼티를 가지고 있다.
  * 이벤트 핸들러 프로퍼티의 키 또한 on 접두사와 이벤트 타입으로 이루어져 있다.
  * 이벤트 핸들러 프로퍼티에 함수를 바인딩하면 이벤트 핸들러가 등록된다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');
    // 이벤트 핸들러 프로퍼티에 이벤트 핸들러를 바인딩
    $button.onclick = function (){
      console.log('button click');
    }
  </script>
</body>
</html>
```

* 이벤트 핸들러를 등록하기 위해서는 이벤트를 발생시킬 객체인 이벤트 타깃과 이벤트의 종류를 나타내는 문자열인 이벤트 타입, 그리고 이벤트 핸들러를 지정할 필요가 있다.

  ![image](https://github.com/user-attachments/assets/a759217b-7a08-4012-adbb-4b984dbe8627)

* 이벤트 핸들러는 대부분 이벤트를 발생시킬 이벤트 타깃에 바인딩한다.
* 하지만 반드시 이벤트 타깃에 이벤트 핸들러를 바인딩해야 하는 것은 아니다.
* 이벤트 핸들러는 이벤트 타깃 또는 전파된 이벤트를 캐치할 DOM 노드 객체에 바인딩한다.
* 이벤트 핸들러 프로퍼티 방식은 이벤트 핸들러 어트리뷰트 방식의 HTML과 자바스크립트가 뒤섞이는 문제를 해결할 수 있다.
* 하지만 이벤트 핸들러 프로퍼티에 하나의 이벤트 핸들러만 바인딩할 수 있다는 단점이 있다.

### 40.3.3 addEventListener 메서드 방식

* DOM Level 2에서 도입된 EventTarget.prototype.addEventListener 메서드를 사용하여 이벤트 핸들러를 등록할 수 있다.
* 앞서 살펴본 이벤트 핸들러 어트리뷰트 방식과 이벤트 핸들러 프로퍼티 방식은 DOM Level 0부터 제공되던 방식이다.

![image](https://github.com/user-attachments/assets/fc38dd22-cc92-4b2c-91ef-b0347e39bb29)

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');
    // 이벤트 핸들러 프로퍼티 방식
    // $button.onclick = function (){
    //   console.log('button click');
    // }

    // addEventListener 메서드 방식
    $button.addEventListener('click', function (){
      console.log('button click');
    });
  </script>
</body>
</html>
```

* 이벤트 핸들러 프로퍼티 방식은 하나 이상의 이벤트 핸들러를 등록할 수 없지만 addEventListener 메서드는 하나 이상의 이벤트 핸들러를 등록할 수 있다.
  * 이때 이벤트 핸들러는 등록된 순서대로 호출된다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');
    // addEventListener 메서드 방식
    $button.addEventListener('click', function (){
      console.log('button click');
    });
    $button.addEventListener('click', function (){
      console.log('button click 2');
    });
  </script>
</body>
</html>
```

## 40.4 이벤트 핸들러 제거

1. addEventListener 메서드 방식
* addEventListener 메서드로 등록한 이벤트 핸들러를 제거하려면 EventTarget.prototype.removeEventListener 메서드를 사용한다.
  * 단, addEventListener 메서드에 전달한 인수와 removeEventListener 메서드에 전달한 인수가 일치하지 않으면 이벤트 핸들러가 제거되지 않는다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');
    const handleClick = () => console.log('button click');
    // 다음과 같이 무명 함수를 이벤트 핸들러로 등록한 경우 제거할 수 없다.
    // 이벤트 핸들러를 제거하려면 이벤트 핸들러의 참조를 변수나 자료구조에 저장하고 있어야 한다.
    // $button.addEventListener('click', function (){
    //   console.log('button click');
    // });
    // 이벤트 핸들러 등록
    $button.addEventListener('click', handleClick);
    // 이벤트 핸들러 제거
    $button.removeEventListener('click', handleClick, true); // 인수가 일치하지 않으므로 실패
    $button.removeEventListener('click', handleClick); // 성공
  </script>
</body>
</html>
```

2. 이벤트 핸들러 프로퍼티 방식
* 이벤트 핸들러 프로퍼티 방식으로 등록한 이벤트 핸들러는 removeEventListener 메서드로 제거할 수 없다.
* 이를 제거하려면 이벤트 핸들러 프로퍼티에 null을 할당한다.

```js
<!DOCTYPE html>
<html>
<body>
  <button>Click me!</button>
  <script>
    const $button = document.querySelector('button');
    const handleClick = () => console.log('button click');
    // 이벤트 핸들러 프로퍼티 방식으로 이벤트 핸들러 등록
    $button.onclick = handleClick;
    // 이벤트 핸들러 제거
    $button.onclick = null;
  </script>
</body>
</html>
```

## 40.5 이벤트 객체

* 이벤트가 발생하면 이벤트에 관련한 다양한 정보를 담고 있는 이벤트 객체가 동적으로 생성된다.
  * 생성된 이벤트 객체는 이벤트 핸들러의 첫 번째 인수로 전달된다.
* 클릭 이벤트에 의해 생성된 이벤트 객체는 이벤트 핸들러의 첫 번째 인수로 전달되어 매개변수 e에 암묵적으로 할당된다.
  * 이는 브라우저가 이벤트 핸들러를 호출할 때 이벤트 객체를 인수로 전달하기 때문이다.
* 따라서 이벤트 객체를 전달받으려면 이벤트 핸들러를 정의할 때 이벤트 객체를 전달받은 매개변수를 명시적으로 선언해야 한다.(e외에 다른 이름도 상관x)

```js
<!DOCTYPE html>
<html>
<body>
  <p>클릭하세요. 클릭한 곳의 좌표가 표시됩니다.</p>
  <em class="message"></em>
  <script>
    const $msg = document.querySelector('.message');
    function showCoords(e){
      $msg.textContent = `clientX: ${e.clientX}, clientY: ${e.clientY}`;
    }
    document.onclick = showCoords;
  </script>
</body>
</html>
```

### 40.5.1 이벤트 객체의 상속 구조

* 이벤트가 발생하면 이벤트 타입에 따라 다양한 타입의 이벤트 객체가 생성된다.
* 이벤트 객체는 다음과 같은 상속 구조를 갖는다.

  ![image](https://github.com/user-attachments/assets/af0220a8-d05d-45a6-9c96-fecb330425e8)


### 40.5.2 이벤트 객체의 공통 프로퍼티

* Event 인터페이스의 이벤트 관련 프로퍼티는 모든 이벤트 객체가 상속받는 공통 프로퍼티다.
* 이벤트 객체의 공통 프로퍼티는 다음과 같다.

![image](https://github.com/user-attachments/assets/23d9ac12-d844-44ae-8044-c50d74a0cf50)
![image](https://github.com/user-attachments/assets/6ad6298e-733d-43dc-b5dd-0130553d44de)


### 40.5.3 마우스 정보 취득

* MouseEvent 타입의 이벤트 객체는 다음과 같은 고유의 프로퍼티를 갖는다.
* 마우스 포인터의 좌표 정보를 나타내는 프로퍼티 : screenX/screenY, clientX/clientY, pageX/pageY, offsetX/offsetY
* 버튼 정보를 나타내는 프로퍼티 : altKey, ctrlKey, shiftKey, button

### 40.5.4 키보드 정보 취득

* keyboardEvent 타입의 이벤트 객체는 altKey, ctrlKey, shiftKey, metaKey, key, keyCode 같은 고유의 프로퍼티를 갖는다.

## 40.6 이벤트 전파

* DOM 트리 상에 존재하는 DOM 요소 노드에서 발생한 이벤트는 DOM 트리를 통해 전파된다.
* \=> 이를 이벤트 전파라고 한다.

![image](https://github.com/user-attachments/assets/c88462c4-f087-4322-bab4-994686e75d21)

* 위 예제에서 ul 요소의 두 번째 자식 요소인 li 요소를 클릭하면 클릭 이벤트가 발생한다.
* 이때 생성된 이벤트 객체는 이벤트를 발생시킨 DOM 요소인 이벤트 타깃을 중심으로 DOM 트리를 통해 전파된다.
* 이벤트 전파는 이벤트 객체가 전파되는 방향에 따라 다음과 같이 3단계로 구분할 수 있다.

```js
![image](https://github.com/user-attachments/assets/33cd3d78-60ad-4950-beb6-a6f809df8b5f)

```
```  
 이벤트 전파 단계
 1.캡처링 단계 : 이벤트가 상위 요소에서 하위 요소 방향으로 전파
 2.타깃 단계 : 이벤트가 이벤트 타깃에 도달
 3.버블링 단계: 이벤트가 하위 요소에서 상위 요소 방향으로 전파
```

* 이벤트 핸들러 어트리뷰트/프로퍼티 방식으로 등록한 이벤트 핸들러는 타깃 단계와 버블링 단계의 이벤트만 캐치할 수 있다.
* addEventListener 메서드 방식으로 등록한 이벤트 핸들러는 캡처링 단계의 이벤트도 선별적으로 캐치할 수 있다.
+ 캡처링 단계의 이벤트를 캐치하려면 addEventListener 메서드의 3번째 인수로 true를 전달해야 한다.
* 이처럼 이벤트는 이벤트를 발생시킨 이벤트 타깃은 물론 상위 DOM 요소에서도 캐치할 수 있다.

## 40.7 이벤트 위임

* 이벤트 위임은 여러 개의 하위 DOM 요소에 각각 이벤트 핸들러를 등록하는 대신 하나의 상위 DOM 요소에 이벤트 핸들러를 등록하는 방법을 말한다.
* 이벤트 전파에서 살펴본 바와 같이 이벤트는 이벤트 타깃은 물론 상위 DOM 요소에서도 캐치할 수 있다.
```  
이벤트 위인의 장점
 → 이벤트 위임을 통해 상위 DOM 요소에 이벤트 핸들러를 등록하면 여러 개의 하위 DOM 요소에 이벤트 핸들러를 등록할 필요가 없다.
 → 동적으로 하위 DOM 요소를 추가하더라도 일일이 추가된 DOM 요소에 이벤트 핸들러를 등록할 필요가 없다.
```

## 40.8 DOM 요소의 기본 동작 조작

### 40.8.1 DOM 요소의 기본 동작 중단

* DOM 요소는 저마다 기본 동작이 있다.
  * ex. a 요소를 클릭하면 href 어트리뷰트에 지정된 링크로 이동
* 이벤트 객체의 preventDefault 메서드는 이러한 DOM 요소의 기본 동작을 중단시킨다.

### 40.8.2 이벤트 전파 방지

* 이벤트 객체의 stopPropagation 메서드는 이벤트 전파를 중지시킨다.

## 40.9 이벤트 핸들러 내부의 this

### 40.9.1 이벤트 핸들러 어트리뷰트 방식

* 다음 예제의 handleClick 함수 내부의 this는 전역 객체 window를 가리킨다.
  * 일반 함수로서 호출되는 함수 내부의 this는 전역 객체를 가리키기 때문
    ![image](https://github.com/user-attachments/assets/f325b6d2-459b-46b2-9236-2fcede58863e)

* 단, 이벤트 핸들러를 호출할 때 인수로 전달한 this는 이벤트를 바인딩한 DOM 요소를 가리킨다.
![image](https://github.com/user-attachments/assets/75b34799-bb99-4269-b1e5-597d757bde9b)

  

### 40.9.2 이벤트 핸들러 프로퍼티 방식과 addEventListener 메서드 방식

* 이벤트 핸들러 프로퍼티 방식과 addEventListener 메서드 방식 모두 이벤트 핸들러 내부의 this는 이벤트를 바인딩한 DOM 요소를 가리킨다.
  * 즉, 이벤트 핸들러 내부의 this는 이벤트 객체의 currentTarget 프로퍼티와 같다.
* 화살표 함수로 정의한 이벤트 핸들러 내부의 this는 상위 스코프의 this를 가리킨다.
  * 화살표 함수는 함수 자체의 this 바인딩을 갖지 않는다.

## 40.10 이벤트 핸들러에 인수 전달

* 이벤트 핸들러 어트리뷰트 방식은 함수 호출문을 사용할 수 있기 때문에 인수를 전달할 수 있다.
* 이벤트 핸들러 프로퍼티 방식과 addEventListener 메서드 방식의 경우 이벤트 핸들러를 브라우저가 호출하기 때문에 함수 호출문이 아닌 함수 자체를 등록하여 인수를 전달할 수 없다.
  * 그러나 이벤트 핸들러 내부에서 함수를 호출하면서 인수를 전달할 수 있다.

## 40.11 커스텀 이벤트

### 40.11.1 커스텀 이벤트 생성

* 개발자의 의도로 생성된 이벤트를 커스텀 이벤트라 한다.
  * 이벤트 생성자 함수는 첫 번째 인수로 이벤트 타입을 나타내는 문자열을 전달받는다.
  * 이때 이벤트 타입을 나타내는 문자열은 기존 이벤트 타입을 사용할 수도 있고, 임의의 문자열을 사용하여 새로운 이벤트 타입을 지정할 수도 있다.
  * 이 경우 일반적으로 CustomEvent 이벤트 생성자 함수를 사용한다.
```
 // KeyboardEvent 생성자 함수로 keyup 이벤트 타입의 커스텀 이벤트 객체를 생성
 const keyboardEvent = new KeyboardEvent('keyup');
 console.log(keyboardEvent.type); // keyup
 // CustomEvent 생성자 함수로 foo 이벤트 타입의 커스텀 이벤트 객체를 생성
 const customEvent = new CustomEvent('foo');
 console.log(customEvent.type); // foo
```


### 40.11.2 커스텀 이벤트 디스패치

* 생성된 커스텀 이벤트는 dispatchEvent 메서드로 디스패치(이벤트를 발생시키는 행위)할 수 있다.
* dispatchEvent 메서드에 이벤트 객체를 인수로 전달하면서 호출하면 인수로 전달한 이벤트 타입의 이벤트가 발생한다.
* 기존 타입이 아닌 임의의 이벤트 타입을 지정하여 커스텀 이벤트 객체를 생성한 경우 반드시addEventListener 메서드 방식으로 이벤트 핸들러를 등록해야 한다.
* 이벤트 핸들러 어트리뷰트/프로퍼티 방식을 사용할 수 없는 이유는 on + 이벤트 타입으로 이루어진 이벤트 핸들러 어트리뷰트/프로퍼티가 요소 노드에 존재하지 않기 때문이다.
  * ex. 'foo' 라는 임의의 이벤트 타입으로 커스텀 이벤트를 생성할 경우 'onfoo'라는 핸들러 어트리뷰트/프로퍼티가 요소 노드에 존재하지 않는다.
