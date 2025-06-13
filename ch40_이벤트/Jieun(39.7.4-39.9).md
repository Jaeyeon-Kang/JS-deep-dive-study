### 39.7.4 data 어트리뷰트와 dataset 프로퍼티
- data 어트리뷰트와 dataset 프로퍼티를 사용하면 HTML 요소에 정의한 사용자 정의 어트리뷰트와 자바스크립트 간에 데이터를 공유할 수 있다.
- data 어트리뷰트는 data- 접두사 다음에 임의의 이름을 붙여 사용한다.
- data 어트리뷰트의 값은 HTMLElement.dataset 프로퍼티로 취득할 수 있다.
- dataset 프로퍼티는 HTML 요소의 모든 data 어트리뷰트의 정보를 제공하는 DOMStringMap 객체를 반환한다.
- DOMStringMap 객체는 data 어트리뷰트의 data- 접두사 다음에 붙인 임의의 이름을 카멜 케이스로 변환한 프로퍼티를 가지고 있다.
- data 어트리뷰트의 data- 접두사 다음에 존재하지 않는 이름을 키로 사용하여 dataset 프로퍼티에 값을 할당하면 HTML 요소에 data 어트리뷰트가 추가된다. 
- 이때 dataset 프로퍼티에 추가한 카멜케이스(fooBar)의 프로퍼티 키는 data 어트리뷰트의 data- 접두사 다음에 케밥케이스(data-foo-bar)로 자동 변경되어 추가된다.
  ```js
  <!DOCTYPE html>
  <html>
  <head>
  </head>
  <body>
    <ul class="users">
      <li id="1" data-user-id="1234" data-role="admin">Lee</li>
      <li id="2" data-user-id="5678" data-role="subscriber">Kim</li>
    </ul>

    <script>
      const users = [...document.querySelector('.users').children];

      const user = users.find(user => user.dataset.userId === '1234');
      console.log(user.dataset.role); // admin

      // data 어트리뷰트 값을 변겅
      user.dataset.role = "subscriber";
      console.log(user.dataset); // DOMStringMap {userId: '1234', role: 'subscriber'}

      // data 어트리뷰트 값을 추가
      user.dataset.age = '30';
      console.log(user.dataset); // DOMStringMap {userId: '1234', role: 'subscriber', age: '30'}
      // → <li id="1" data-user-id="1234" data-role="subscriber" data-age="30">Lee</li>
    </script>
  </body>
  </html>
  ```

## 39.8 스타일
### 39.8.1 인라인 스타일 조작
- HTMLElement.prototype.style 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 요소 노드의 인라인 스타일을 취득하거나 추가 또는 변경한다.
- style 프로퍼티를 참조하면 CSSStyleDeclaration 타입의 객체를 반환하며, CSSStyleDeclaration 객체의 프로퍼티는 카멜 케이스를 따른다. 예를 들어, CSS 프로퍼티 background-color에 대응하는 CSSStyleDeclaration 객체의 프로퍼티는 backgroundColor다.
  ```js
  <!DOCTYPE html>
  <html>
  <body>
    <div style="color: red;">Hello World</div>

    <script>
      const $div = document.querySelector('div');
      console.log($div.style);

      $div.style.color = 'blue';

      $div.style.width = '100px';
      $div.style.backgroundColor = 'yellow';
    </script>
  </body>
  </html>
  ```
- 케밥 케이스의 CSS 프로퍼티를 그대로 사용하려면 객체의 마침표 표기법 대신 대괄호 표기법을 사용한다.
  ```js
  $div.style['background-color'] = 'yellow';
  ```

### 39.6.2 클래스 조작
- .으로 시작하는 클래스 선택자를 사용하여 CSS class를 미리 정의한 다음, HTML 요소의 class 어트리뷰트 값을 변경하여 HTMl 요소의 스타일을 변경할 수도 있다.
- HTML 요소의 class 어트리뷰트를 조작하려면 class 어트리뷰트에 대응하는 요소 노드의 DOM 프로퍼티를 사용한다.
- class 어트리뷰트에 대응하는 DOM 프로퍼티는 class가 아니라 className과 classList다.

**className**
- Element.prototype.className 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 HTML 요소의 class 어트리뷰트 값을 취득하거나 변경한다.
  ```js
  <!DOCTYPE html>
  <html>
  <head>
    <style>
      .box {
        width: 100px; 
        height: 100px;
        background-color: antiquewhite;
      }
      .red { color: red; }
      .blue { color: blue; }
    </style>
  </head>
  <body>
    <div class="box red">Hello World</div>

    <script>
      const $box = document.querySelector('.box');
      console.log($box.className); // box red

      // className 프로퍼티는 문자열을 반환하므로 공백으로 구분된 여러 개의 클래스를 반환하는 경우 다루기가 불편하다.
      $box.className = $box.className.replace('red', 'blue');
      console.log($box.className); // box blue
    </script>
  </body>
  </html>
  ```

**classList**
- Element.prototype.classList 프로퍼티는 class 어트리뷰트의 정보를 담은 DOMTokenList 객체를 반환한다.
  ```js
  console.log($box.classList); 
  // DOMTokenList(2) [0: "box", 1: "blue", length: 2, value: "box blue"]
  ```
- DOMTokenList 객체는 class 어트리뷰트의 정보를 나타내는 컬렉션 객체로서 유사 배열 객체이면서 이터러블이다.
- DOMTokenList 객체는 다음과 같이 유용한 메서드들을 제공한다.

  **add(...className)**
  - add 메서드는 인수로 전달한 1개 이상의 문자열을 class 어트리뷰트 값으로 추가한다.
    ```js
    $box.classList.add('foo'); // → class="box red foo"
    $box.classList.add('bar', 'baz'); // → class="box red foo bar baz"
    ```
  **remove(...className)**
  - remove 메서드는 인수로 전달한 1개 이상의 문자열과 일치하는 클래스를 class 어트리뷰트에서 삭제한다. 인수로 전달한 문자열 과 일치하는 클래스가 class 어트리뷰트에 없으면 에러 없이 무시된다.
    ```js
    $box.classList.remove('foo'); // → class="box red bar baz"
    $box.classList.remove('bar', 'baz'); // → class="box red"
    $box.classList.remove('x'); // → class="box red"
    ```
  **item(index)**
  - item 메서드는 인수로 전달한 index에 해당하는 클래스를 class 어트리뷰트에서 반환한다. 예를 들어, index가 0이면 첫 번째 클래스를 반환하고 index가 1이면 두 번째 클래스를 반환한다.
    ```js
    $box.classList.item(0); // → "box"
    $box.classList.item(1); // → "red"
    ```
  **contain(className)**
  - contains 메서드는 인수로 전달한 문자열과 일치하는 클래스가 class 어트리뷰트에 포함되어 있는지 확인한다.
    ```js
    $box.classList.contains('box'); // → true
    $box.classList.contains('blue'); // → false
    ```
  **replace(oldClassName, newClassName)**
  - replace 메서드는 class 어트리뷰트에서 첫 번째 인수로 전달한 문자열을 두 번째 안수로 전달한 문자열로 변경한다.
    ```js
    $box.classList.replace('red', 'blue'); // → class="box blue"
    ```
  **toggle(className[, force])**
  - toggle 메서드는 class 어트리뷰트에 인수로 전달한 문자열과 일치하는 클래스가 존재하면 제거하고, 존재하지 않으면 추가한다.
    ```js
    $box.classList.toggle('foo'); // → class="box blue foo"
    $box.classList.toggle('foo'); // → class="box blue"
    ```
  - 두 번째 인수로 불리언 값으로 평가되는 조건식을 전달할 수 있다. 
  - 조건식의 평가 결과가 true이면 class 어트리뷰트에 강제로 첫 번째 인수로 전달받은 문자열을 추가하고、false이면 class 어트리뷰트에서 강제로 첫 번째 인수로 전달받은 문자열을 제거한다.
    ```js
    $box.classList.toggle('foo', true); // → class="box blue foo"
    $box.classList.toggle('foo', false); // → class="box blue"
    ```
- 이 밖에도 forEach, entries, keys, values, supports 메서드를 제공한다.
  
### 39.8.3 요소에 적용되어 있는 CSS 스타일 참조 
