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

* DOM에 새로운 요소들을 추가하면 **기존 노드들이 이동**함

### 39.6.7. 노드 복사

* `Node.prototype.cloneNode([deep: true | false])`
* 노드를 **복사하여 반환**함

  * `false` (기본값): **얕은 복사** → 노드 자신만 복사 (텍스트 노드도 없음)
  * `true`: **깊은 복사** → 모든 **자손 노드 포함**

### 39.6.8. 노드 교체

* `Node.prototype.replaceChild(newChild, oldChild)`
* 호출한 노드의 자식 노드를 **다른 노드로 교체**
* `oldChild`는 DOM에서 제거됨
* `oldChild`가 자식 노드가 아니면 **DOMException 발생**

### 39.6.9. 노드 삭제

* `Node.prototype.removeChild(child)`
* 전달한 노드를 **DOM에서 삭제**
* 삭제 대상은 **호출한 노드의 자식 노드여야 함**

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

#### 39.7.2. HTML 어트리뷰트 조작

* `Element.prototype.getAttribute(attributeName)` : 어트리뷰트 값 조회
* `Element.prototype.setAttribute(attributeName, attributeValue)` : 어트리뷰트 값 설정
* `Element.prototype.hasAttribute(attributeName)` : 어트리뷰트 존재 여부 확인
* `Element.prototype.removeAttribute(attributeName)` : 어트리뷰트 삭제

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
