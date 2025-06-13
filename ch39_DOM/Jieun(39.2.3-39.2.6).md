### 39.2.3 class를 이용한 요소 노드 취득
- Document.prototype/Element.prototype.getElementByClassName 메서드는 인수로 전달한 class 어트리뷰트 값을 갖는 모든 요소 노드들을 탐색하여 반환한다.
- 인수로 전달한 class 값은 공백으로 구분하여 여러 개의 class를 지정할 수 있다.
  ```html
  <!DOCTYPE html>
  <html>
    <body>
      <ul>
        <li class="fruit apple">Apple</li>
        <li class="fruit banana">Banana</li>
        <li class="fruit orange">Orange</li>
      </ul>
   
      <script>
        const $elems = document.getElementByClassName('fruit');
       [...$elems].forEach(elem => { elem.style.color = 'red'; });
     
       // class 값이 'fruit apple'인 요소 노드를 모두 탐색
       const $apples = document.getElementByClassName('fruit apple');
       [...$apples].forEach(elem => { elem.style.color = 'blue'; });
      </script>
   
     <ul id="colors">
        <li class="red">Red</li>
        <li class="blue">Blue</li>
        <li class="green">Green</li>
      </ul>
      <div class="blue">Blue</div>
   
     <script>
       const $bluesFromDocument = document.getElementsByClassName('blue');
        console.log($bluesFromDocument);
        // HTMLCollection(2) [li.blue, div.blue]

        const $colors = document.getElementById('colors');
        const $bluesFromColors = $colors.getElementByClassName('blue');
        console.log($bluesFromColors); // HTMLCollection [li.blue]
     </script>
    </body>
  </html>
  ```

### 39.2.4 CSS 선택자를 이용한 요소 노드 취득
  ```css
  * { ... }
  p { ... }
  #foo { ... }
  .foo { ... }
  input[type=text] { ... }
  div p { ... }
  /* 자식 선택자: div 요소의 자식 요소 중 p 요소를 모두 선택 */
  div > p { ... }
  /* 인접 형제 선택자: p 요소의 형제 요소 중에 p 요소 바로 뒤에 위치하는 ul 요소를 선택 */
  p + ul { ... }
  /* 일반 형제 선택자: p 요소의 형제 요소 중에 p 요소 뒤에 위치하는 ul 요소를 모두 선택 */
  P ~ ul { ... }
  /* 가상 클래스 선택자: hover 상태인 a 요소를 모두 선택 */
  a:hover { ... }
  /* 가상 요소 선택자: p 요소의 콘텐츠의 앞에 위치하는 공간을 선택 
     일반적으로 content 프로퍼티와 함께 사용된다. */
  p::before { ... }
  ```
  ```html
  <!DOCTYPE html>
  <html>
    <body>
      <ul>
        <li class="apple">Apple</li>
        <li class="banana">Banana</li>
        <li class="orange">Orange</li>
      </ul>
   
      <script>
        const $elems = document.querySelector('.banana');
        
       const $elems2 = document.querySelectorAll('ul > li');
        console.log($elems2); // NodeList(3) [li.apple, li.banana, li.orange]
      
      const $all = document.querySelectorAll('*');
      // → NodeList(8) [html, head, body, ul, li.apple, li.banana, li.orange, script]
      </script>

    </body>
  </html>
  ```
- CSS 선택자 문법을 사용하는 queryselector, querySelectorAll 메서드는 getElementByld, getElementsBy*** 메서드보다 다소 느린 것으로 알려져 있다.
- 하지만 CSS 선택자 문법을 사용하여 좀 더 구체적인 조건으로 요소 노드를 취득할 수 있고 일관된 방식으로 요소 노드를 취득할 수 있다는 장점이 있다. 
- 따라서 id 어트리뷰트가 있는 요소 노드를 취득하는 경우에는 getElementByld 메서드를 사용하고 그 외의 경우에는 queryselector, querySelectorAll 메서드를 사용하는 것을 권장한다.

### 39.2.5 특정 요소 노드를 취득할 수 있는지 확인
- Element.prototype.matches 메서드는 인수로 전달한 CSS 선택자를 통해 특정 요소 노드를 취득할 수 있는지 확인한다.
  ```html
  <!DOCTYPE html>
  <html>
    <body>
      <ul id="fruits">
        <li class="apple">Apple</li>
        <li class="banana">Banana</li>
        <li class="orange">Orange</li>
      </ul>
   
      <script>
        const $apple = document.querySelector('.apple');
        
       console.log($apple.matches('#fruits > li.apple')); // true
        console.log($apple.matches('#fruits > li.banana')); // false
      </script>

    </body>
  </html>
  ```

### 39.2.6 HTMLCollection과 NodeList
- HTMLCollection과 NodeList는 DOM API가 여러 개의 결과값을 반환하기 위한 DOM 컬렉션 객체다.
- 유사 배열 객체이면서 이터러블이기 때문에 for...of문으로 순회할 수 있고, 스프레드 문법을 사용하여 배열로 변환할 수 있다.
- HTMLCollection은 언제나 live 객체로 동작하며, NodeList는 대부분의 경우 non-live 객체로 동작하지만 경우에 따라 live 객체로 동작할 때가 있다.
  - live 객체 (살아있는 객체): 노드 객체의 상태 변화를 실시간으로 반영
  - non-live 객체: 노드 객체의 상태 변화를 실시간으로 반영하지 않고 과거의 정적 상태를 유지

**HTMLCollection**
- getElementsByTagName, getElementsByClassName 메서드가 반환하는 HTMLCollection 객체는 노드 객체의 상태 변화를 실시간으로 반영하는 살아 있는 DOM 컬렉션 객체다.
  ```html
  <!DOCTYPE html>
  <html>
  <head>
    <style>
      .red { color: red; }
      .blue { color: blue; }
    </style>
  </head>
  <body>
    <ul id="fruits">
      <li class="red">Apple</li>
      <li class="red">Banana</li>
      <li class="red">Orange</li>
    </ul>

    <script>
      const $elems = document.getElementsByClassName('red');
      console.log($elems); // HTMLCollection(3) [li.red, li.red, li.red]

      for(let i=0; i<$elems.length; i++) {
        $elems[i].className = 'blue';
      }

      console.log($elems); // HTMLCollection [li.red]
    </script>
  </body>
  </html>
  ```
- 위 예제가 실행되면 모든 li 요소의 class 값이 'blue'로 변경될 것이라고 예상했지만, 두번째 li 요소만 class 값이 변경되지 않는다.
- HTMLCollection 객체는 실시간으로 노드 객체의 상태 변경을 반영하여 요소를 제거할 수 있기 때문에 HTMLCollection 객체를 for 문으로 순회하면서 노드 객체의 상태를 변경해야 할 때 주의해야 한다.
- for 문을 역방향으로 순회하거나, while문을 사용하여 HTMLCollection 객체에 노드 객체가 남아 있지 않을 때까지 무한 반복하는 방법으로 회피할 수 있다.
  ```javascript
  for(let i=$elems.length-1; i>=0; i--) {
    $elems[i].className = 'blue';
  }
  
  let i=0;
  while ($elems.length > i) {
    $elems[i].className = 'blue';
  }
  ```
- 부작용을 발생시키는 원인인 HTMLCollection 객체를 사용하지 않고 HTMLCollection 객체를 배열로 변환하면 유용한 배열의 고차 함수(forEach. map, filter, reduce 등)를 사용할 수 있다.
  ```javascript
  [...$elems].forEach(elem => elem.className = 'blue');
  ```

**NodeList**
- HTMLCollection 객체의 부작용을 해결하기 위해 getElementsByTagName, getElementsByClassName 메서드 대신 querySelectorAll 메서드를 사용하는 방법도 있다.
- querySelectorAll 메서드는 DOM 컬렉션 객체인 NodeList 객체를 반환한다.
- 이때 NodeList 객체는 실시간으로 노드 객체의 상태 변경을 반영하지 않는 non-live 객체다.
- 하지만 childNodes 프로퍼티가 반환하는 NodeList 객체는 HTMLCollection 객체와 같이 실시간으로 노드 객체의 상태 변경을 반영하는 live 객체로 동작하므로 주의가 필요하다.
  ```html
  <!DOCTYPE html>
  <html>
  <head>
  </head>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>

    <script>
      const $fruits = document.getElementById('fruits');
      
      const { childNodes } = $fruits;
      console.log(childNodes instanceof NodeList); // true

      // 공백 텍스트 노드를 포함해 모두 5개다.
      console.log(childNodes); // NodeList(5) [text, li, text, li, text]

      for(let i=0; i<childNodes.length; i++) {
        // removechild 메서드가 호출될 때마다 NodeList 객체인 childNodes가 실시간으로 변경된다.
        // 따라서 첫 번째, 세 번째, 다섯 번째 요소만 삭제된다.
        $fruits.removeChild(childNodes[i]);
      }

      console.log(childNodes); // NodeList(2) [li, li]
    </script>
  </body>
  </html>
  ```
- 이처럼 HTMLCollection이나 NodeList 객체는 예상과 다르게 동작할 때가 있어 다루기 까다롭고 실수하기 쉽기 때문에 HTMLCollection이나 NodeList 객체를 배열로 변환하여 사용하는 것을 권장한다.
  ```javascript
  const $fruits = document.getElementById('fruits');
    
  const { childNodes } = $fruits;
    
  [...childNodes].forEach(childNode => {
    $fruits.removeChild(childNode);
  });

  console.log(childNodes); // NodeList []
  ```
