- 브라우저가 HTML, CSS, JS로 작성된 텍스트 문서를 어떻게 파싱하여 브라우저에 렌더링하는지 살펴보자.
```
- 파싱(parsing)
: 파싱은 프로그래밍 언어의 문법에 맞게 작성된 텍스트 문서를 읽어 들여 실행하기 위해 텍스트 문서의 문자열을 토큰으로 분해하고, 토큰에 문법적 의미와 구조를 반영하여 트리 구조의 자료구조인 파스 트리를 생성하는 일련의 과정을 말한다.
- 렌더링(rendering)
: 렌더링은 HTML, CSS, JS로 작성된 문서를 파싱하여 브라우저에 시각적으로 출력하는 것을 말한다.
```

![image](https://github.com/user-attachments/assets/3d4fa2eb-66c0-4999-99d7-e92c4c8c23b9)

- 브라우저는 다음과 같은 과정을 거쳐 렌더링을 수행한다.
  - 브라우저는 HTML, CSS, JS, 이미지, 폰트 파일 등 렌더링에 필요한 리소스를 요청하고 서버로부터 응답을 받는다.
  - 브라우저의 렌더링 엔진은 서버로부터 응답된 HTML과 CSS를 파싱하여 DOM과 CSSOM을 생성하고 이들을 결합하여 렌더 트리를 생성한다.
  - 브라우저의 자바스크립트 엔진은 서버로부터 응답된 자바스크립트를 파싱하여 AST(Abstract Syntax Tree) 를 생성하고 바이트코드로 변환하여 실행한다. 이때 자바스크립트는 DOM API를 통해 DOM이나 CSSOM을 변경할 수 있다. 변경된 DOM과 CSSOM은 다시 렌더 트리로 결합된다.
  - 렌더 트리를 기반으로 HTML 요소의 레이아웃을 계산하고 브라우저 화면에 HTML 요소를 페인팅한다.

## 38.1 요청과 응답
- 브라우저의 핵심 기능은 필요한 리소스를 서버에 요청하고 서버로부터 응답을 받아 브라우저에 시각적으로 렌더링하는 것이다.

  - 서버에 요청을 전송하기 위해 브라우저는 주소창을 제공한다.
  - 브라우저의 주소창에 URL을 입력하고 엔터 키를 누르면 URL의 호스트 이름이 DNS를 통해 IP 주소로 변환되고 이 IP 주소를 갖는 서버에게 요청을 전송한다.
  - 예를 들어, 브라우저의 주소창에 https://poiemaweb.com 을 입력하고 엔터 키를 누르면 루트 요청이 poiemaweb.com 서버로 전송된다. 루트 요청에는 암묵적으로 index.html을 응답하도록 기본 설정되어 있다.

- 만약 index.html이 아닌 다른 정적 파일을 서버에 요청하려면 브라우저의 주소창에https://poiemaweb.com/assets/data/data.json 과 같이 요청할 정적 파일의 경로와 파일 이름을 URI의 호스트 뒤의 패스에 기술하여 서버에 요청한다.
- 반드시 브라우저의 주소창을 통해 서버에게 정적 파일만을 요청할 수 있는 것은 아니다. 자바스크립트를 통해 동적으로 서버에 정적/동적 데이터를 요청할 수도 있다. (ajax, REST API)
- 요청과 응답은 개발자 도구의 Network 패널에서 확인할 수 있다.

## 38.2 HTTP 1.1 과 HTTP 2.0
- HTTP는 웹에서 브라우저와 서버가 통신하기 위한 프로토콜이다.
- HTTP 1.1 과 HTTP 2.0의 차이점
  - HTTP 1.1은 기본적으로 커넥션당 하나의 요청과 응답만 처리한다. 즉, 여러 개의 요청을 한 번에 전송할 수 없고 응답 또한 마찬가지다.
  - 리소스의 동시 전송이 불가능한 구조이므로 요청할 리소스의 개수에 비례하여 응답 시간도 증가하는단점이 있다.

  ![image](https://github.com/user-attachments/assets/642543a9-6107-4215-b6a0-9bb20cc0db4c)

  - HTTP/2는 커넥션당 여러 개의 요청과 응답, 즉 다중 요청/응답이 가능하다.
  - 따라서 HTTP 2.0 은 여러 리소스의 동시 전송이 가능하므로 HTTP 1.1에 비해 페이지 로드 속도가 약 50% 정도 빠르다고 알려져 있다.
 
  ![image](https://github.com/user-attachments/assets/cc3c49f7-e232-4685-a731-bb4edaf8bcc8)


  ## 38.3 HTML 파싱과 DOM 생성
  - 브라우저의 요청에 의해 서버가 응답한 HTML 문서는 문자열로 이루어진 순수한 텍스트다.
  - 순수한 텍스트인 HTML 문서를 브라우저에 시각적인 픽셀로 렌더링하려면 HTML 문서를 브라우저가 이해할 수 있는 자료구조로 변환하여 메모리에 저장해야 한다.
  - 브라우저의 렌더링 엔진은 다음 그림과 같은 과정을 통해 응답받은 HTML 문서를 파싱하여 브라우저가 이해할 수 있는 자료구조인 DOM을 생성한다.
 
  ![image](https://github.com/user-attachments/assets/e72f7db9-ba74-4bfe-8485-5b4a771fcb63)

  - 서버에 존재하던 HTML 파일이 브라우저의 요청에 의해 응답된다.
    - 이때 서버는 브라우저가 요청한 HTML 파일을 읽어 들여 메모리에 저장한 다음 메모리에 저장된 바이트를 인터넷을 경유하여 응답한다.
  - 브라우저는 서버가 응답한 HTML 문서를 바이트 형태로 응답받는다.
    - 그리고 응답된 바이트 형태의 HTML 문서는 meta 태그의 charset 어트리뷰트에 의해 지정된 인코딩 방식을 기준으로 문자열로 변환된다.
  - 문자열로 변환된 HTML 문서를 읽어 들여 문법적 의미를 갖는 코드의 최소 단위인 토큰들로 분해한다.
  - 각 토큰들을 객체로 변환하여 노드들을 생성한다.
    - 노드는 이후 DOM을 구성하는 기본 요소가 된다.
  - HTML 문서는 HTML 요소들의 집합으로 이루어지며 HTML 요소는 중첩 관계를 갖는다.
    - 이러한 HTML 요소 간의 부자 관계를 반영하여 모든 노드들을 트리 자료구조로 구성한다.
    - 이 노드들로 구성된 트리 구조를 DOM이라 부른다.
  - 즉, DOM은 HTML 문서를 파싱한 결과물이다.
 
  ## 38.4 CSS 파싱과 CSSOM 생성
  - 렌더링 엔진은 HTML을 처음부터 한 줄씩 순차적으로 파싱하여 DOM을 생성해 나간다.
  - 이처럼 렌더링 엔진은 DOM을 생성해 나가다가 CSS를 로드하는 link 태그나 style 태그를 만나면 DOM 생성을 일시 중단한다.
  - 그리고 link 태그의 href 어트리뷰트에 지정된 CSS 파일을 서버에 요청하여 로드한 CSS파일이나 style 태그 내의 CSS를 HTML과 동일한 파싱 과정을 거치며 해석하여 CSSOM을 생성한다.
  - 이후 CSS 파싱을 완료하면 HTML 파싱이 중단된 지점부터 다시 HTML을 파싱하기 시작하여 DOM 생성을 재개한다.
 
    ![image](https://github.com/user-attachments/assets/a5a0adb3-65b9-4d11-a210-cf57d6a90890)

    
  ## 38.5 렌더 트리 생성
  - 렌더링 엔진은 서버로부터 응답된 HTML과 CSS를 파싱하여 각각 DOM과 CSSOM를 생성한다.
    - 그리고 DOM과 CSSOM은 렌더링을 위해 렌더 트리로 결합된다.
  - 렌더 트리는 렌더링을 위한 트리 구조의 자료구조다.
    - 따라서 브라우저 화면에 렌더링되지 않는 노드(ex. meta, script 태그)와 CSS에 비해 비표시되는 노드들은 포함하지 않는다. 다시 말해, 렌더 트리는 브라우저 화면에 렌더링되는 노드만으로 구성된다.
    ![image](https://github.com/user-attachments/assets/8e173cdf-0a2e-4c61-be72-9c843827a34a)
  - 이후 완성된 렌더 트리는 각 HTML 요소의 레이아웃을 계산하는 데 사용되며 브라우저 화면에 픽셀을 렌더링하는 페인팅 처리에 입력된다.
    ![image](https://github.com/user-attachments/assets/9f9edb27-7e38-41b5-9455-366d9d5ef224)
  - 레이아웃 계산과 페인팅을 다시 실행하는 리렌더링은 비용이 많이 드는, 성능에 악영향을 주는 작업이다. 따라서 가급적 리렌더링이 빈번하게 발생하지 않도록 주의할 필요가 있다.

   ## 38.6 자바스크립트 파싱과 실행
  - DOM은 HTML 문서의 구조와 정보뿐만 아니라 HTML 요소와 스타일 등을 변경할 수 있는 프로그래밍 인터페이스로서 DOM API를 제공한다.
    - 즉, 자바스크립트 코드에서 DOM API를 사용하면 이미 생성된 DOM을 동적으로 조작할 수 있다.
  - 렌더링 엔진은 HTML을 한 줄씩 순차적으로 파싱하며 DOM을 생성해 나가다가 script 태그를 만나면 DOM 생성을 일시 중단한다.
  - 그리고 자바스크립트 파일을 서버에 요청하여 자바스크립트 코드를 파싱하기 위해 자바스크립트 엔진에 제어권을 넘긴다.
    - 이후 자바스크립트 파싱과 실행이 종료되면 렌더링 엔진으로 다시 제어권을 넘겨 HTML 파싱이 중단된 지점부터 다시 HTML 파싱을 시작하여 DOM 생성을 재개한다.
  - 자바스크립트 엔진은 자바스크립트를 해석하여 AST(추상 구문 트리)를 생성한다. 그리고 AST를 기반으로 인터프리터가 실행할 수 있는 중간 코드인 바이트코드를 생성하여 실행한다.
 
    ![image](https://github.com/user-attachments/assets/593e84d8-908a-422e-895d-d3e78de6fda6)

    ## 38.7 리플로우와 리페인트
    - 만약 자바스크립트 코드에 DOM이나 CSSOM을 변경하는 DOM API가 사용된 경우 DOM이나 CSSOM이 변경된다.
    - 이때 변경된 DOM과 CSSOM은 다시 렌더 트리로 결합되고 변경된 렌더 트리를 기반으로 레이아웃과 페인트 과정을 거쳐 브라우저의 화면에 다시 렌더링한다.
      - 이를 리플로우, 리페인트라 한다.
    - 리플로우 : 레이아웃 계산을 다시 하는 것
    - 리페인트 : 재결합된 렌더 트리를 기반으로 다시 페인트를 하는 것

     ![image](https://github.com/user-attachments/assets/16cf1a08-3a2c-4f4a-aded-f74733054c0f)

    #### 리플로우/리페인트를 최소화하기 위한 꿀팁들
    ```js
    // documentFragment 사용

    const frag = document.createDocumentFragment();

    for (let i = 0; i < 1000; i++) {
      const li = document.createElement('li');
      li.textContent = `item ${i}`;
      frag.appendChild(li);
    }
    
    // 이 때까진 실제 DOM에 아무 영향 없음
    document.querySelector('ul').appendChild(frag);

    //    → 위 코드는 li 1000개를 미리 documentFragment에 모아놨다가 한 번에 진짜 DOM에 붙임.
    // 장점: appendChild를 1000번 하는 대신 1번만 DOM에 접근하므로 리플로우도 1번만 발생함.
    ```
 
    ```js
    // display: none 사용

    const box = document.getElementById('box');
    box.style.display = 'none'; // 화면에서 숨김
    
    // 여러 style 속성 변경
    box.style.width = '400px';
    box.style.height = '200px';
    box.style.backgroundColor = 'skyblue';
    
    box.style.display = 'block'; // 다시 보여줌

    //  이러면 중간의 변경 사항들이 리플로우 없이 누적되고 마지막 display: block에서 한 번에 반영됨.
    ```
  
    #### ✅ documentFragment 역할 in React
    React 자체가 컴포넌트를 가상 DOM 트리로 먼저 구성해서 마지막에 한 번에 실제 DOM에 패치함. 사실상 React는 자동으로 documentFragment 비슷한 구조를 내부에서 사용한다고 보면 됨.
    ```js
    const List = ({ items }) => {
    return (
            <ul>
              {items.map(item => <li key={item.id}>{item.text}</li>)}
            </ul>
          );
        };

    ```
    - 여기서 items.length가 1000이어도 React는 Virtual DOM에서 먼저 다 계산한 다음 실제 DOM에는 diff 비교 후 바뀐 부분만 갱신함. (React 18부터는 concurrent rendering으로 더 최적화됨)
      

    ## 38.8 자바스크립트 파싱에 의한 HTML 파싱 중단
 
    - 렌더링 엔진과 자바스크립트 엔진은 병렬적으로 파싱을 실행하지 않고 직렬적으로 파싱을 수행한다.
      ![image](https://github.com/user-attachments/assets/8b100294-4322-4cdc-a4ea-beff48087316)
    - 이처럼 브라우저는 동기적으로, 즉 위에서 아래 방향으로 순차적으로 HTML, CSS, JS를 파싱하고 실행한다.
    - 이것은 script 태그의 위치에 따라 HTML 파싱이 블로킹되어 DOM 생성이 지연될 수 있다는 것을 의미한다.
      - 따라서 script 태그의 위치는 중요한 의미를 갖는다.
    - body 가장 아래에 자바스크립트를 위치시키는 것은 좋은 아이디어다.
    - 그 이유는 다음과 같다.
      - DOM이 완성되지 않은 상태에서 자바스크립트가 DOM을 조작하면 에러가 발생할 수 있다.
      - 자바스크립트 로딩/파싱/실행으로 인해 HTML 요소들의 렌더링에 지장받는 일이 발생하지 않아 페이지 로딩 시간이 단축된다.
     
    ## 38.9 script 태그의 async/defer 어트리뷰트
    - 앞에서 살펴본 자바스크립트 파싱에 의한 DOM 생성이 중단되는 문제를 근본적으로 해결하기 위해 HTML5부터 script 태그에 async와 defer 어트리뷰트가 추가되었다.
    - async와 defer 어트리뷰트를 사용하면 HTML 파싱과 외부 자바스크립트 파일의 로드가 비동기적으로 동시에 진행된다.
    - 하지만 자바스크립트 실행 시점에 차이가 있다.
   
    ### async 어트리뷰트
    - HTML 파싱과 외부 자바스크립트 파일의 로드가 비동기적으로 동시에 진행된다.
      - 단, 자바스크립트의 파싱과 실행은 자바스크립트 파일의 로드가 완료된 직후 진행되며,
      - 이때 HTML 파싱이 중단된다.
    - 여러 개의 script 태그에 async 어트리뷰트를 지정하면 script 태그의 순서와는 상관없이 로드가 완료된 자바스크립트부터 먼저 실행되므로 순서가 보장되지 않는다.
      - 따라서 순서 보장이 필요한 script 태그에는 async 어트리뷰트를 지정하지 않아야 한다.
      ![image](https://github.com/user-attachments/assets/4b175b4f-82bb-414d-8b5f-556a7f72ecff)

    ### defer 어트리뷰트
    - async 어트리뷰트와 마찬가지로 HTML 파싱과 외부 자바스크립트 파일의 로드가 비동기적으로 동시에 진행된다.
      - 단, 자바스크립트의 파싱과 실행은 HTML 파싱이 완료된 직후, 즉 DOM 생성이 완료된 직후 진행된다.
      - 따라서 DOM 생성이 완료된 이후 실행되어야 할 자바스크립트에 유용하다.
     
      ![image](https://github.com/user-attachments/assets/d9f5b301-004d-4ed2-a8ac-1c02feda3695)


    
    | script 종류        | HTML 파싱 멈춤? | 실행 시점                              | 순서 보장 |
    | ---------------- | ----------- | ---------------------------------- | ----- |
    | 일반 `<script>`    | ✅ O         | 다운로드 직후 (즉시)                       | ✅ O   |
    | `<script async>` | ❌ X         | 다운로드 직후 (즉시)                       | ❌ X   |
    | `<script defer>` | ❌ X         | HTML 파싱 완료 후 (DOMContentLoaded 직전) | ✅ O   |
    
    
    <img src="https://github.com/user-attachments/assets/3a3c7558-a3dc-4ebf-8042-00757c5c7334" width="700" alt="defer 설명 이미지">








