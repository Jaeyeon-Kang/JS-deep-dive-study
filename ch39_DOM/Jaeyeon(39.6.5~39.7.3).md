### 39.6.5. 노드 삽입

#### 마지막 노드로 추가

* `Node.prototype.appendChild`
* 삽입 위치 지정 불가, **마지막 자식 노드**로 추가

#### 지정한 위치에 노드 삽입

* `Node.prototype.insertBefore(newNode, childNode)`
* 첫 번째 요소 노드를 두 번째 노드 앞에 삽입
* 두 번째 노드가 호출한 노드의 **자식 노드가 아니면 DOMException 발생**
* 두 번째 노드가 `null`인 경우 **마지막 자식 노드로 추가됨** (`=appendChild`와 동일)

```js
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 요소 노드 생성
    const $li = document.createElement('li');

    // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
    $li.appendChild(document.createTextNode('Orange'));

    // $li 요소 노드를 첫 번째 요소 노드를 두 번째 노드 앞에 삽입
    $fruits.insertBefore($li, $fruits.lastElementChild);
    // Apple - Orange - Banana
  </script>
</html>
```

### 39.6.6. 노드 이동

* DOM에 이미 존재하는 노드를 appendChild 또는 insertBefore 메서드를 사용하여 DOM에 다시 추가하면 현재 위치에서 노드를 제거하고 새로운 위치에 노드를 추가한다. 즉, 노드가 이동한다.

```js
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
      <li>Orange</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits')

    // 이미 존재하는 요소 노드를 취득
    const [$apple, $banana] = $fruits.children;

    // 이미 존재하는 $apple 요소 노드를 #fruits 요소 노드의 마지막 노드로 이동
    $fruits.appendChild($apple);

    $fruits.insertBefore($banana, $fruits.lastElementChild);
    // Orange - Banana - Apple
  </script>
</html>
```


### 39.6.7. 노드 복사

* `Node.prototype.cloneNode([deep: true | false])`
* 노드를 **복사하여 반환**함

  * `false` (기본값): **얕은 복사** → 노드 자신만 복사 (텍스트 노드도 없음)
  * `true`: **깊은 복사** → 모든 **자손 노드 포함**

```js
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');
    const $apple = $fruits.firstElementChild;

    // $apple 요소를 얕은 복사하여 사본을 생성. 텍스트 노드가 없는 사본이 생성
    const $shallowClone = $apple.cloneNode();
    // 사본 요소 노드에 텍스트 추가
    $shallowClone.textContent = 'Banana';
    // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
    $fruits.appendChild($shallowClone);

    // #fruits 요소를 깊은 복사하여 모든 자손 노드가 포함된 사본을 생성
    const $deepClone = $fruits.cloneNode(true);
    // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
    $fruits.appendChild($deepClone);
  </script>
</html>
```
![](https://i.imgur.com/Qvvd8aA.gif)

### 39.6.8. 노드 교체

* `Node.prototype.replaceChild(newChild, oldChild)`
* 호출한 노드의 자식 노드를 **다른 노드로 교체**
* `oldChild`는 DOM에서 제거됨
* `oldChild`가 자식 노드가 아니면 **DOMException 발생**
```js
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 기존 노드와 교체할 요소 노드를 생성
    const $newChild = document.createElement('li');
    $newChild.textContent = 'Banana';

    // #fruits 요소 노드의 첫 번째 요소 노드를 $newChild 요소 노드로 교체
    $fruits.replaceChild($newChild, $fruits.firstElementChild);
  </script>
</html>
```
![image](https://github.com/user-attachments/assets/14cb3438-968d-45ed-ad55-1f2595fb8741)


### 39.6.9. 노드 삭제

* `Node.prototype.removeChild(child)`
* 전달한 노드를 **DOM에서 삭제**
* 삭제 대상은 **호출한 노드의 자식 노드여야 함**
```js
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // #fruits 요소 노드의 마지막 요소를 삭제
    $fruits.removeChild($fruits.lastElementChild);
  </script>
</html>
```
![image](https://github.com/user-attachments/assets/0c4947c8-b50e-4e2e-bb6a-41dac885da10)



### 39.7. 어트리뷰트

#### 39.7.1. 어트리뷰트 노드와 `attributes` 프로퍼티

* HTML 요소는 여러 개의 어트리뷰트(속성)를 가질 수 있음
* 어트리뷰트는 HTML 요소의 시작 태그에 "이름='값'" 형식으로 정의

  * 예: `<input id="user" type="text" value="ungmo2">`
* 공통 어트리뷰트 (글로벌 어트리뷰트): `id`, `class`, `style`, 이벤트 핸들러 등
* 특정 요소 전용 어트리뷰트: `input`의 `type`, `value`, `checked` 등
* HTML 어트리뷰트당 하나의 어트리뷰트 노드 생성됨
* 모든 어트리뷰트 노드는 NamedNodeMap 객체에 저장됨

  * `Element.prototype.attributes`: 읽기 전용, 모든 어트리뷰트 노드 참조를 포함한 객체 반환
```js
<!DOCTYPE html>
<html>
  <body>
    <input id="user" type="text" value="sunny" />
    <script>
      // 요소 노드의 attribute 프로퍼티는 요소 노드의 모든 어트리뷰트 노드의 참조가 담긴
      // NamedNodeMap 객체를 반환한다.
      const { attributes } = document.getElementById('user');
      console.log(attributes);
	  //  NameNodeMap {0: id, 1: type, 2: value, id: id, type: type, value: value, length: 3}

      // 어트리뷰트 값 취득
      console.log(attributes.id.value); // user
      console.log(attributes.type.value); // text
      console.log(attributes.value.value); // sunny
    </script>
  </body>
</html>
```
![image](https://github.com/user-attachments/assets/5d4c4039-e574-4a7a-8f3c-af03d6a54364)

#### 39.7.2. HTML 어트리뷰트 조작

* `Element.prototype.getAttribute(attributeName)` : 어트리뷰트 값 조회
* `Element.prototype.setAttribute(attributeName, attributeValue)` : 어트리뷰트 값 설정

```js
<!DOCTYPE html>
<html>
  <body>
    <input id="user" type="text" value="sunny" />
    <script>
      const $input = document.getElementById('user');

      // value 어트리뷰트 값을 취득
      const inputValue = $input.getAttribute('value');
      console.log(inputValue); // sunny

      // value 어트리뷰트 값을 변경
      $input.setAttribute('value', 'foo');
      console.log($input.getAttribute('value')); // foo
    </script>
  </body>
</html>
```
![image](https://github.com/user-attachments/assets/231cde1d-fff7-4b56-a7fa-b87914c32bfe)


* `Element.prototype.hasAttribute(attributeName)` : 어트리뷰트 존재 여부 확인
* `Element.prototype.removeAttribute(attributeName)` : 어트리뷰트 삭제

```js
<!DOCTYPE html>
<html>
  <body>
    <input id="user" type="text" value="sunny" />
    <script>
      const $input = document.getElementById('user');

      // value 어트리뷰트의 존재 확인
      if ($input.hasAttribute('value')) {
        // value 어트리뷰트 삭제
        $input.removeAttribute('value');
      }

      // value 어트리뷰트가 삭제되었다.
      console.log($input.hasAttribute('value')); // false
    </script>
  </body>
</html>
```
![image](https://github.com/user-attachments/assets/27d000d6-23c8-40a0-8d5a-bca9f83f62d7)


#### 39.7.3. HTML 어트리뷰트 vs DOM 프로퍼티

* 요소 노드 객체는 HTML 어트리뷰트에 대응하는 DOM 프로퍼티를 가짐

* **DOM 프로퍼티**

  * `getter/setter` 존재하는 접근자 프로퍼티
  * 사용자의 입력에 따른 최신 상태 값을 반영

* **HTML 어트리뷰트**

  * HTML 요소의 초기 상태를 지정
  * 첫 렌더링 전까지는 어트리뷰트 값 = DOM 프로퍼티 값
  * 이후 상태 변화는 DOM 프로퍼티에 반영됨

* 어트리뷰트와 DOM 프로퍼티의 대응 예:

  * `id` 어트리뷰트 ↔ `id` 프로퍼티
  * `value` 어트리뷰트 ↔ `value` 프로퍼티
  * `class` 어트리뷰트 ↔ `className`, `classList` 프로퍼티
  * `for` 어트리뷰트 ↔ `htmlFor` 프로퍼티

* 대응 없음 예:

  * `td` 요소의 `colspan` ↔ 대응하는 프로퍼티 없음
  * `textContent` 프로퍼티 ↔ 대응하는 어트리뷰트 없음

* 어트리뷰트는 **대소문자 구별 없음**, DOM 프로퍼티는 **카멜 케이스**

* `getAttribute()`로 얻은 값은 **항상 문자열**

* DOM 프로퍼티는 **타입 다양** (예: `checked`는 Boolean)
