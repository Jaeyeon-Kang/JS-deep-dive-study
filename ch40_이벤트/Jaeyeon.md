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
