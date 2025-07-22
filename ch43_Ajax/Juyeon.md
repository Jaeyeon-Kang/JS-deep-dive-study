# 43장 Ajax
## 43.1 Ajax란?
: Ajax란 자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식을 말한다.

Ajax의 등장으로 서버로부터 웹페이지의 변경에 필요한 데이터만 비동기 방식으로 전송받아 웹페이지를 변경할 필요가 없는 부분은 다시 렌더링하지 않고, 변경할 필요가 있는 부분만 한정적으로 렌더링하는 방식이 가능해졌다.

전통적 방식의 단점
1. 이전 웹페이지와 차이가 없어서 변경할 필요가 없는 부분까지 포함된 완전한 HTML을 서버로부터 매번 다시 전송받기 때문에 불 필요한 데이터 통신이 발생한다.
2. 변경할 필요가 없는 부분까지 처음부터 다시 렌더링한다. 이로 인해 화면 전환이 일어나면 화면이 순간적으로 깜박이는 현상이 발 생한다.
3. 클라이언트와 서버와의 통신이 동기 방식으로 동작하기 때문에 서버로부터 응답이 있을 때까지 다음 처리는 블로킹된다.

Ajax의 장점
1. 변경할 부분을 갱신하는 데 필요한 데이터만 서버로부터 전송받기 때문에 불필요한 데이터 통신이 발생하지 않는다.
2. 변경할 필요가 없는 부분은 다시 렌더링하지 않는다. 따라서 화면이 순간적으로 깜박이는 현상이 발생하지 않는다.
3. 클라이언트와 서버와의 통신이 비동기 방식으로 동작하기 때문에 서버에게 요청을 보낸 이후 블로킹이 발생하지 않는다.

## 43.2 JSON
: JSON은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이다. 자바스크립트에 종속되지 않는 언어 독립형 데이터 포맷으로, 대부분의 프로그래밍 언어에서 사용할 수 있다.

### 43.2.1 JSON 표기 방식
JSON은 자바스크립트의 객체 리터럴과 유사하게 키와 값으로 구성된 순수한 텍스트다.
JSON의 키는 반드시 큰따옴표(작은따옴표 사용 불가)로 묶어야 한다. 
값은 객체 리터럴과 같은 표기법을 그 대로 사용할 수 있다. 하지만 문자열은 반드시 큰따옴표(작은따옴표 사용 불가)로 묶어야 한다.

### 43.2.2 JSON.stringify
JSON.stringify 메서드는 객체를 JSON 포맷의 문자열로 변환한다.
클라이언트가 서버로 객체를 전송하려면 객체를 문자열화해야 하는데 이를 직렬화라 한다.
  클라이언트 ---------------> 서버
  객체 —> 직렬화(객체 문자열화) —> 문자열
JSON.stringify 메서드는 객체뿐만 아니라 배열도 JSON 포맷의 문자열로 변환한다.

### 43.2.3 JSON.parse
JSON.parse 메서드는 JSON 포맷의 문자열을 객체로 변환한다.
서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열이다. 이 문자열을 객체로서 사용하려면 JSON 포맷의 문자열을 객체화해야 하는데 이를 역직렬화라 한다.
  서버 ---------------> 클라이언트
                     —> 문자열 역직렬화(문자열 객체화)

배열이 JSON 포맷의 문자열로 변환되어 있는 경우 JSON.pars은는문자열을 배열 객체로 변환한다. 배열의 요소가 객체인 경우 배열의 요소까지 객체로 변환한다.
  const todos = [
    { id: 1, content: 'HTML', completed: false },
    { id: 2, content: 'CSS', completed: true },
    { id: 3, content: 'JavaScript', competed: false }
  ];

## 43.3 XMLHttpRequest
브라우저는 주소창이나 HTML의 form 태그 또는 a 태그를 통해 HTTP 요청 전송 기능을 기본 제공한다.
자바스크립트를 사용하여 HTTP 요청을 전송하려면 XMLHttpRequest 객체를 사용한다.
Web API인 XMLHttpRequest 객체는 HTTP 요청 전송과 HTTP 응답 수신을 위한 다양한 메서드와 프로퍼티를 제공한다.

### 43.3.1 XMLHttpRequest 객체 생성
XMLHttpRequest 객체는 XMLHttpRequest 생성자 함수'를 호출하여 생성한다.
XMLHttpRequest 객체는 브라우저에서 제공하는 Web API이므로 브라우저 환경에서만 정상적으로 실행된다.

### 43.3.2 XMLHttpRequest 객체의 프로퍼티와 메서드
*교재 p.822 참고

### 43.3.3 HTTP 요청 전송
HTTP 요청을 전송하는 경우 다음 순서를 따른다.
1. XMLHttpRequest.prototype.open 메서드로 HTTP 요청을 초기화한다.
2. 필요에 따라 XMLHttpRequest. prototype. setRequestHeader 메서드로 특정 HTTP 요청의 헤더 값을 설정한다.
3. XMLHttpRequest. prototype.send 메서드로 HTTP 요청을 전송한다.

XMLHttpRequest.prototype.open
호출방법: xhr.open(method, url[, async])
method - HTTP 요청 메서드("GET", "POST", "PUT", "DELETE" 등)
url    - HTTP 요청을 전송할 URL
async  - 비동기 요청 여부 옵션으로 기본값은 true이며, 비동기 방식으로 동작한다.

XMLHttpRequest.prototype.send
GET 요청 메서드의 경우 데이터를 URL의 일부분인 쿼리 문자열(query string)로 서버에 전송한다.
POST 요청 메서드의 경우 데이터(페이로드payload)를 요청몸체(request body)에 담아 전송한다.

XMLHttpRequest.prototype.setRequestHeader
자주 사용하는 HTTP 요청 헤더는 Content-type과 Accept가 있다.
Content-type은 요청 몸체에 담아 전송할 데이터의 MIME 타입의 정보를 표현한다. 자주 사용되는 MIME 타입은 다음과 같다.
*미디어 타입 (Multipurpose Internet Mail Extensions 또는 MIME type로도 알려져 있음)이란 문서, 파일 또는 바이트 집합의 성격과 형식을 나타냅니다.

MIME 타입      서브타입
text        - text/plain, text/html, text/css, text/javascript
application - application/json, application/x-www-form-urlencode
multipart   - multipart/formed-data

### 43.3.4 HTTP 응답 처리
서버가 전송한 응답을 처리하려면 XMLHttpRequest 객체가 발생시키는 이벤트를 캐치해야 한다.
HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티 값이 변경된 경우 발생하는 readystatechange 이벤트를 캐치하여 다음과 같이 HTTP 응답을 처리할 수 있다.
readystatechange 이벤트 대신 load 이벤트를 캐치해도 좋다. load 이벤트는 HTTP 요청이 성공적으로 완료된 경우 발생한다. 따라서 load 이벤트를 캐치하는 경우 xhr.readyState가 XMLHttpRequest.DONE인지 확인할 필요가 없다.
