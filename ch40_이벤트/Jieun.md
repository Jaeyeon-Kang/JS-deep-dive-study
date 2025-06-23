## 40.7 이벤트 위임
- 이벤트 위임은 여러 개의 하위 DOM 요소에 각각 이벤트 핸들러를 등록하는 대신 하나의 상위 DOM 요소에 이벤트 핸들러를 등록하는 방법을 말한다.
  ```js
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <style>
      #fruits {
        display: flex;
        list-style-image: none;
        padding: 0;
      }

      #fruits li {
        width: 100px;
        cursor: pointer;
      }

      #fruits .active {
        color: red;
        text-decoration: underline;
      }
    </style>
  </head>
  <body>
    <nav>
      <ul id="fruits">
        <li id="apple" class="actice">Apple</li>
        <li id="banana">Banana</li>
        <li id="orange">Orange</li>
      </ul>
    </nav>
    <div>선택된 내비게이션 아이템: <em class="msg">apple</em></div>

    <script>
      const $fruits = document.getElementById('fruits');
      const $msg = document.querySelector('.msg');

      function activate({ target }) {
        // 이벤트를 발생시킨 요소(target)가 ul#fruits의 자식 요소가 아니라면 무시한다.
        if(!target.matches('#fruits > li')) return;

        [...$fruits.children].forEach($fruit => {
          $fruit.classList.toggle('active', $fruit === target)
          $msg.textContent = target.id;
        })
      }

      // 이벤트 위임
      $fruits.onclick = activate;
    </script>
  </body>
  </html>
  ```
- 이벤트 객체의 currentTarget 프로퍼티는 언제나 변함없이 $fruits 요소를 가리키지만 이벤트 객체의 target 프로퍼티는 실제로 이벤트를 발생시킨 DOM 요소를 가리킨다.

## 40.8 DOM 요소의 기본 동작 조작
### 40.8.1 DOM 요소의 기본 동작 중단
- DOM 요소는 저마다 기본 동작이 있는데 preventDefault 메서드를 사용하면 DOM 요소의 기본 동작을 중단시킨다.
  ```js
  <a href="https://www.google.com">go</a>
  <input type="checkbox" />
  <script>
    document.querySelector('a').onclick = e => {
      e.preventDefault();
    }

    document.querySelector('input[type=checkbox]').onclick = e => {
      e.preventDefault();
    }
  </script>
  ```

### 40.8.2 이벤트 전파 방지
- 이벤트 객체의 stopPropagation 메서드는 이벤트 전파를 중지시킨다.
  ```js
  <div class="container">
    <button class="btn1">Button 1</button>
    <button class="btn2">Button 2</button>
    <button class="btn3">Button 3</button>
  </div>

  <script>
    document.querySelector('.container').onclick = ({ target }) => {
      if(!target.matches('.container > button')) return;
      target.style.color = 'red';
    }

    document.querySelector('.btn2').onclick = e => {
      e.stopPropagation(); // 이벤트 전파 중단
      e.target.style.color = 'blue';
    }
  </script>
  ```

## 40.9 이벤트 핸들러 내부의 this
### 40.9.1 이벤트 핸들러 어트리뷰트 방식
- 이벤트 핸들러를 호출할 떄 인수로 전달한 this는 이벤트를 바인딩한 DOM 요소를 가리킨다.
  ```js
  <button onclick="handleClick(this)">Click me</button>

  <script>
    function handleClick(button) {
      console.log(button); // 이벤트를 바인딩한 button 요소
      console.log(this); // window
    }
  </script>
  ```

### 40.9.2 이벤트 핸들러 프로퍼티 방식과 addEventListener 메서드 방식
- 이벤트 핸들러 프로포티 방식과 addEventListener 메서드 방식 모두 이벤트 핸들러 내부의 this는 이벤트를 바인딩한 DOM 요소를 가리킨다.
- 이벤트 핸들러 내부의 this는 이벤트 객체의 currentTarget 프로퍼티와 같다.
  ```js
  <button class="btn1">0</button>
  <button class="btn2">0</button>

  <script>
    const $button1 = document.querySelector('.btn1');
    const $button2 = document.querySelector('.btn2');

    // 이벤트 핸들러 프로퍼티 방식
    $button1.onclick = function (e) {
      console.log(this); // $button1
      console.log(e.currentTarget); // $button1

      ++this.textContent;
    }

    // addEventListener 메서드 방식
    $button2.addEventListener('click', function (e) {
      console.log(this); // $button2
      console.log(e.currentTarget); // $button2

      ++this.textContent;
    })
  </script>
  ```
- 화살표 함수로 정의한 이벤트 핸들러 내부의 this는 상위 스코프의 this를 가리킨다. 화살표 함수는 함수 자체의 this 바인딩을 갖지 않는다.
  ```js
  // 이벤트 핸들러 프로퍼티 방식
  $button1.onclick = e => {
    console.log(this); // window
    console.log(e.currentTarget); // $button1

    // this는 window를 가리키므로 window.textContext에 NaN(undefined + 1)을 할당한다.
    ++this.textContent;
  }

  // addEventListener 메서드 방식
  $button2.addEventListener('click', e => {
    console.log(this); // window
    console.log(e.currentTarget); // $button2

    // this는 window를 가리키므로 window.textContext에 NaN(undefined + 1)을 할당한다.
    ++this.textContent;
  })
  ```
- 클래스에서 이벤트 핸들러를 바인딩하는 경우 this에 주의해야 한다.
  ```js
  <button class="btn">0</button>

  <script>
    class App {
      constructor() {
        this.$button = document.querySelector('.btn');
        this.count = 0;

        // increase 메서드 내부의 this가 인스턴스를 가리키도록 한다.
        this.$button.onclick = this.increase.bind(this);
      }

      increase() {
        // bind 메서드를 사용하여 this를 전달하지 않는다면
        // 이벤트 핸들러 increase 내부의 this는 DOM요소(this.$button)를 가리킨다.
        this.$button.textContent = ++this.count;
      }
    }

    new App();
  </script>
  ```
- 화살표 함수를 이벤트 핸들러로 등록하여 이벤트 핸들러 내부의 this가 인스턴스를 가리키도록 할 수도 있다.
- 다만 이때 이벤트 핸들러 increase는 프로토타입 메서드가 아닌 인스턴스 메서드가 된다.
  ```js
  <button class="btn">0</button>

  <script>
    class App {
      constructor() {
        this.$button = document.querySelector('.btn');
        this.count = 0;

        this.$button.onclick = this.increase;
      }

      increase = () => this.$button.textContent = ++this.count;
    }
    new App();
  </script>
  ```

## 40.10 이벤트 핸들러에 인수 전달
- 함수에 인수를 전달할 때 이벤트 핸들러 어트리뷰트 방식은 함수 호출문을 사용하므로 전달할 수 있다.
- 이벤트 핸들러 프로퍼티 방식과 addEventListener 메서드 방식의 경우 아래와 같이 이벤트 핸들러 내부에서 함수를 호출하면서 인수를 전달할 수 있다.
  ```js
  <label>User name <input type="text" /></label>
  <em class="message"></em>

  <script>
    const MIN_USER_NAME_LENGTH = 5;
    const $input = document.querySelector('input[type=text]');
    const $msg = document.querySelector('.message');

    const checkUserNameLength = min => {
      $msg.textContent = $input.value.length < min ? `${min}자 이상 입력해주세요` : '';
    };

    // 이벤트 핸들러 내부에서 함수를 호출하면서 인수를 전달
    $input.onblur = () => {
      checkUserNameLength(MIN_USER_NAME_LENGTH);
    }
  </script>
  ```

## 40.11 커스텀 이벤트
### 40.11.1 커스텀 이벤트 생성
- 개발자의 의도로 생성된 이벤트를 커스텀 이벤트라 한다.
- 이벤트 생성자 함수는 첫 번째 인수로 이벤트 타입을 나타내는 문자열을 전달받는다. 문자열은 기존 이벤트 타입을 사용할 수도 있고, 임의의 문자열을 사용하여 새로운 이벤트 타입을 지정할 수도 있다.
  ```js
  const keyboardEvent = new KeyboardEvent('keyup');
  console.log(keyboardEvent.type); // keyup

  const customEvent = new CustomEvent('foo');
  console.log(customEvent.type); // foo
  ```
- 생성된 커스텀 이벤트 객체는 버블링되지 않으며 preventDefault 메서드로 취소할 수도 없다.
  ```js
  const customEvent = new MouseEvent('click');
  console.log(customEvent.type); // click
  console.log(customEvent.bubbles); // false
  console.log(customEvent.cancelable); // false

  // 프로퍼티 변경 및 추가
  const customEvent2 = new MouseEvent('click', {
    bubbles: true,
    cancelable: true,
    // 이벤트 고유의 프로퍼티 값을 지정할 수도 있다.
    clientX: 50,
    clientY: 100
  });
  console.log(customEvent2.bubbles); // true
  console.log(customEvent2.cancelable); // true
  console.log(customEvent2.clientX); // 50
  console.log(customEvent2.clientY); // 100
  ```

### 40.11.2 커스텀 이벤트 디스패치
- 생성된 커스텀 이벤트는 dispatchEvent 메서드로 디스패치(이벤트를 발생시키는 행위) 할 수 있다.
- 일반적으로 이벤트 핸들러는 비동기 처리 방식으로 동작하지만 dispatchEvent 메서드는 이벤트 핸들러를 동기 처리 방식으로 호출한다.
- 다시 말해, dispatchEvent 메서드를 호출하면 커스텀 이벤트에 바인딩된 이벤트 핸들러를 직접 호출하는 것과 같다.
  ```js
  <button class="btn">Click me</button>

  <script>
    const $button = document.querySelector('.btn');

    // 커스텀 이벤트를 디스패치하기 이전에 이벤트 핸들러를 등록해야 한다.
    $button.addEventListener('click', e => {
      console.log(e); // MouseEvent
      alert(`${e} Clicked!`);
    });

    // 커스텀 이벤트 생성
    const customEvent = new MouseEvent('click');

    // 커스텀 이벤트 디스패치(동기 처리), click 이벤트가 발생한다.
    $button.dispatchEvent(customEvent);
  </script>
  ```
- 기존 이벤트 타입이 아닌 임의의 이벤트 타입을 지정하여 이벤트 객체를 생성하는 경우 일반적으로 CustomEvent 이벤트 생성자 함수를 사용한다.
- CustomEvent 이벤트 생성자 함수에는 두 번째 인수로 이벤트와 함께 전달하고 싶은 정보를 담은 detail 프로퍼티를 포함하는 객체를 전달할 수 있다.
  ```js
  <button class="btn">Click me</button>

  <script>
    const $button = document.querySelector('.btn');

    $button.addEventListener('foo', e => {
      alert(e.detail.message);
    });

    // CustomEvent 생성자 함수로 foo 이벤트 타입의 커스텀 이벤트 객체 생성
    const customEvent = new CustomEvent('foo', {
      detail: { message: 'Hello' } // 이벤트와 함께 전달하고 싶은 정보
    });

    // 커스텀 이벤트 디스패치
    $button.dispatchEvent(customEvent);
  </script>
  ```
 