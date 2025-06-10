## 39.3 노드 탐색

Node, Element 인터페이스는 DOM트리 상의 노드를 탐색할 수 있도록 **트리 탐색 프로퍼티**를 제공한다.

![Image](https://github.com/user-attachments/assets/421b263b-a9ef-4f4f-923d-16cbaa39f598)

Node.prototype은 parentNode, previousSibling, firstChild, childNodes 프로퍼티를 제공한다.

Element.prototype은 previousElementSibling, nextElementSibling, children 프로퍼티를 제공한다.

**노드 탐색 프로퍼티는 setter 없이 getter만 존재하여 참조만 가능한 읽기 전용 접근자 프로퍼티다.**


### ✅ `Node.prototype`에 있는 탐색 프로퍼티 (모든 노드에서 사용 가능)

텍스트 노드, 주석 노드, 요소 노드 등 **모든 노드 타입**에서 공통적으로 제공되는 프로퍼티

- `parentNode`
- `previousSibling`
- `nextSibling`
- `firstChild`
- `lastChild`
- `childNodes` (NodeList)

### ✅ `Element.prototype`에 있는 탐색 프로퍼티 (요소 노드에서만 사용 가능)

HTML 요소들에만 적용됨. 예: `<div>`, `<span>`, `<p>` 같은 태그들

- `previousElementSibling`
- `nextElementSibling`
- `children` (HTMLCollection)
- `firstElementChild`
- `lastElementChild`

## 🔸 주요 노드 종류 요약

| 노드 종류 | 이름 | 설명 |
| --- | --- | --- |
| `Element` 노드 | 요소 노드 | 우리가 알고 있는 `<div>`, `<p>`, `<a>` 같은 HTML 태그들 |
| `Text` 노드 | 텍스트 노드 | 요소 안의 글자(텍스트)를 담고 있는 노드 |
| `Comment` 노드 | 주석 노드 | HTML 주석 `<!-- 주석 -->` 을 담고 있는 노드 |


### 39.3.1 공백 텍스트 노드

HTML 요소 사이의 스페이스, 탭, 줄바꿈(개행) 등의 공백 문자는 텍스트 노드를 생성한다. 이를 공백 텍스트 노드라 한다. 

![Image](https://github.com/user-attachments/assets/74ff6de4-9337-4ccc-9782-6d4c62d6d98e)

노드를 탐색할 때는 공백 문자가 생성한 공백 텍스트 노드에 주의해야 한다.

| 상황 | 위험 요소 | 해결 방법 |
| --- | --- | --- |
| `childNodes` | 공백 텍스트 노드가 섞임 | `children` 사용 |
| `firstChild`, `lastChild` | 공백 때문에 엉뚱한 노드 반환 | `firstElementChild` 사용 |
| `forEach` 같은 반복 | 예상치 못한 `Text` 노드 처리 | `children` 반복 사용 또는 `node.nodeType === 1`로 필터 |


### 39.3.2 자식 노드 탐색

자식 노드를 탐색하기 위해서 다음과 같은 노드 탐색 프로퍼티를 사용한다.

| 프로퍼티 | 설명 |
| --- | --- |
| Node.prototype.childNodes | 자식 노드를 모두 탐색하여 DOM 컬렉션 객체인 NodeList에 담아 반환한다. childNodes 프로퍼티가 반환한 NodeList에는 요소 노드뿐만 아니라 텍스트 노드도 포함되어 있을 수 있다. |
| Element.prototype.children | 자식 노드 중에서 요소 노드만 모두 탐색하여 DOM 컬렉션 객체인 HTMLCollection에 담아 반환한다. children 프로퍼티가 반환한 HTMLCollection에는 텍스트 노드가 포함되지 않는다. |
| Node.prototype.firstChild | 첫 번째 자식 노드를 반환한다. firstChild 프로퍼티가 반환한 노드는 텍스트 노드이거나 요소 노드다. |
| Node.prototype.lastChild | 마지막 자식 노드를 반환한다. lastChild 프로퍼티가 반환한 노드는 텍스트 노드이거나 요소 노드다. |
| Element.prototype.firstElementChild | 첫 번째 자식 요소 노드를 반환한다. firstElementChild 프로퍼티는 요소 노드만 반환한다. |
| Element.prototype.lastElementChild | 마지막 자식 요소 노드를 반환한다. lastElementChild 프로퍼티는 요소 노드만 반환한다. |


### 39.3.3 자식 노드 존재 확인

자식 노드가 존재하는지 확인하려면 Node.prototype.hasChildNodes 메서드를 사용한다. hasChildNodes 메서드는 자식 노드가 존재하면 true, 자식 노드가 존재하지 않으면 false를 반환한다. 단, hasChildNodes 메
서드는 childNodes 프로퍼티와 마찬가지로 텍스트 노드를 포함하여 자식 노드의 존재를 확인한다.

자식 노드 중에 텍스트 노드가 아닌 요소 노드가 존재하는지는 확인하려면 hasChildNodes 메서드 대신
children.length 또는 Element 인터페이스의 childElementCount 프로퍼티를 사용한다.


### 39.3.4 요소 노드의 텍스트 노드 탐색

요소 노드의 텍스트 노드는 요소 노드의 자식 노드다. 따라서 요소 노드의 텍스트 노드는 firstChild 프로퍼티로 접근할 수 있다. firstChild 프로퍼티는 첫 번째 자식 노드를 반환한다. firstChild 프로퍼티가 반환한
노드는 텍스트 노드이거나 요소 노드다.


### 39.3.5 부모 노드 탐색

부모 노드를 탐색하려면 Node.prototype.parentNode 프로퍼티를 사용한다. 텍스트 노드는 DOM 트리의 최종단 노드인 리프 노드 이므로 부모 노드가 텍스트 노드인 경우는 없다.


### 39.3.6 형제 노드 탐색

부모 노드가 같은 형제 노드를 탐색하려면 다음과 같은 노드 탐색 프로퍼티를 사용한다.
단, 어트리뷰트 노드는 요소 노드와 연결되어 있지만 부모 노드가 같은 형제 노드가 아니기 때문에 반환되지 않는다.

Node.prototype.previousSibling - 부모 노드가 같은 형제 요소 노드 중에서 자신의 **이전 형제 노드**를 탐색하여 반환한다.

Node.prototype.nextsibling - 부모 노드가 같은 형제 요소 노드 중에서 자신의 **다음 형제 노드**를 탐색하여 반환한다.

Element.prototype.previousElementSibling - 부모 노드가 같은 형제 요소 노드 중에서 자신의 **이전 형제 요소노드**를 탐색하여 반환한다.

Element.prototype.nextElementSibling - 부모 노드가 같은 형제 요소 노드 중에서 자신의 **다음 형제 요소노드**를 탐색하여 반환한다.


## 39.4 노드 정보 취득

노드 객체에 대한 정보를 취득하려면 다음과 같은 노드 정보 프로퍼티를 사용한다.

| 프로퍼티 | 설명 |
| --- | --- |
| Node.prototype.nodeType | 노드 객체의 종류, 즉 노드 타입을 나타내는 상수를 반환한다.<br>노드 타입의 상수는 Node에 정의되어 있다.<br>- Node.ELEMENT_NODE: 요소 노드 타입을 나타내는 상수 1을 반환 <br>- Node.TEXT_NODE: 텍스트 노드 타입을 나타내는 상수 3을 반환<br>- Node.DOCUMENT_NODE: 문서 노드 타입을 나타내는 상수 9를 반환 |
| Node.prototype.nodeName | 노드의 이름을 문자열로 반환한다. <br>- 요소노드: 대문자 문자열로 태그 이름(”UL”, “LI” 등)을 반환 <br>- 텍스트 노드: 문자열 “#text”를 반환 <br>- 문서노드: 문자열 “#document”를 반환 |


## 39.5 요소 노드의 텍스트 조작

### 39.5.1 nodeValue

Node.prototype.nodeValue 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티다. 따라서
nodeValue 프로퍼티는 참조와 할당 모두 가능하다.

노드 객체의 nodeValue 프로퍼티를 참조하면 노드 객체의 값을 반환한다. 노드 객체의 값이란 텍스트 노드
의 텍스트다. 따라서 텍스트 노드가 아닌 노드, 즉 문서 노드나 요소 노드의 nodeValue 프로퍼티를 참조하면
null을 반환한다.

텍스트 노드의 nodeValue 프로퍼티에 값을 할당하면 텍스트 노드의 값, 즉 텍스트를 변경할 수 있다.

**<요소 노드의 텍스트 변경 순서>**

1. 텍스트를 변경할 요소 노드를 취득한 다음, 취득한 요소 노드의 텍스트 노드를 탐색한다. 텍스트 노드는 요소 노드의 자식 노드이므로 firstChild 프로퍼티를 사용하여 탐색한다.
2. 탐색한 텍스트 노드의 nodeValue 프로퍼티를 사용하여 텍스트 노드의 값을 변경한다.


### 39.5.2 textContent

Node.prototype.textcontent 프로퍼티는 setter와 getter 모두 존재하는 접근자 프로퍼티로서 요소 노드의
텍스트와 모든 자손 노드의 텍스트를 모두 취득하거나 변경한다.

요소 노드의 textcontent 프로퍼티를 참조하면 요소 노드의 콘텐츠 영역(시작 태그와 종료 태그 사이) 내의
텍스트를 모두 반환한다. 다시 말해, 요소 노드의 childNodes 프로퍼티가 반환한 모든 노드들의 텍스트 노드
의 값, 즉 텍스트를 모두 반환한다. 이때 HTML 마크업은 무시된다.

요소 노드의 textContent 프로퍼티에 문자열을 할당하면 요소 노드의 모든 자식 노드가 제거되고 할당한 문자열이 텍스트로 추가된다. 이때 할당한 문자열에 HTML 마크업이 포함되어 있더라도 문자열 그대로 인식되어 텍스트로 취급된다. 즉, HTML 마크업이 파싱되지 않는다.

textContent 프로퍼티와 유사한 동작을 하는 innerText 프로퍼티가 있다. innerText 프로퍼티는 다음과 같은 이유로 사용하지 않는 것이 좋다.

- innerText 프로퍼티는 CSS에 순종적이다. 예를 들어, innerText 프로퍼티는 CSS에 의해 비표시(visibility: hidden;) 로 지정된 요소 노드의 텍스트를 반환하지 않는다.
- innerText 프로퍼티는 CSS를 고려해야 하므로 textContent 프로퍼티보다 느리다.
