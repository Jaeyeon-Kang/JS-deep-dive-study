## 43.1 Ajax란?
- Ajax란 자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식을 말한다.
- Ajax는 브라우저에서 제공하는 Web API인 XMLHttpRequest 객체를 기반으로 동작하며, XMLHttpRequest는 HTTP 비동기 통신을 위한 메서드와 프로퍼티를 제공한다.
- 이전의 웹페이지는 html 태그로 시작해서 html 태그로 끝나는 완전한 HTML을 서버로부터 전송받아 웹페이지 전체를 처음부터 다시 렌더링하는 방식으로 동작했다.
- Ajax는 서버로부터 웹페이지의 변경에 필요한 데이터만 비동기 방식으로 전송받아 변경이 필요한 부분만 렌더링하는 방식으로 빠를 퍼포먼스와 부드러운 화면 전환이 가능해졌다.

|전통적인 웹페이지 방식의 단점|Ajax의 장점|
|---|---|
|변경할 필요가 없는 부분까지 포함된 완전한 HTML을 서버로부터 매번 다시 전송받기 때문에 불필요한 데이터 통신이 발생한다.|변경할 부분을 갱신하는 데 필요한 데이터만 서버로부터 전송받기 때문에 불필요한 데이터 통신이 발생하지 않는다.|
|변경할 필요가 없는 부분까지 처음부터 다시 렌더링한다. 이로 인해 화면 전환이 일어나면 화면이 순간적으로 깜박이는 현상이 발생한다.|변경할 필요가 없는 부분은 다시 렌더링하지 않는다. 따라서 화면이 순간적으로 깜박이는 현상이 발생하지 않는다.|
|클라이언트와 서버와의 통신이 동기 방식으로 동작하기 때문에 서버로부터 응답이 있을 때까지 다음 처리는 블로킹된다.|클라이언트와 서버와의 통신이 비동기 방식으로 동작하기 때문에 서버에게 요청을 보낸 이후 블로킹이 발생하지 않는다.|

## 43.2 JSON
- JSON은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이다.

### 43.2.1 JSON 표기 방식
- JSON은 자바스크립트의 객체 리터럴과 유사하게 키와 값으로 구성된 순수 텍스트다.
  ```json
  {
    "name": "Lee",
    "age": 20,
    "alive": true,
    "hobby": ["traveling", "tennis"]
  }
  ```

### 43.2.2 JSON.stringify
- JSON.stringify 메서드는 객체 또는 배열을 JSON 포맷의 문자열로 변환한다.
- 클라이언트가 서버로 객체를 전송하려면 객체를 문자열화해야 하는데 이를 직렬화라고 한다.
  ```js
  const obj = {
    "name": "Lee",
    "age": 20,
    "alive": true,
    "hobby": ["traveling", "tennis"]
  };

  const json = JSON.stringify(obj);
  console.log(typeof json, json);
  // string {"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}

  // 객체를 JSON 포맷의 문자열로 변환하면서 들여쓰기 한다.
  const prettyJson = JSON.stringify(obj, null, 2);
  console.log(typeof prettyJson, prettyJson);
  /*
  string {
    "name": "Lee",
    "age": 20,
    "alive": true,
    "hobby": [
      "traveling",
      "tennis"
    ]
  }
  */

  // replacer 함수, 값의 타입이 Number이면 필터링되어 반환되지 않는다.
  function filter(key, value) {
    // undefined: 반환하지 않음
    return typeof value === 'number' ? undefined : value;
  }

  const strFilteredObject = JSON.stringify(obj, filter, 2);
  console.log(typeof strFilteredObject, strFilteredObject);
  /*
  string {
    "name": "Lee",
    "alive": true,
    "hobby": [
      "traveling",
      "tennis"
    ]
  }
  */
  ```

### 43.2.3 JSON.parse
- JSON.parse 메서드는 JSON 포맷의 문자열을 객체로 변환한다.
- 역직렬화
  ```js
  const str = '{"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}';
  const parsed = JSON.parse(str);
  console.log(typeof parsed, parsed);
  // object {name:"Lee", age:20, alive:true, hobby:["traveling","tennis"]}
  ```

## 43.3 XMLHttpRequest
- 브라우저는 주소창이나 HTML의 form 태그 또는 a 태그를 통해 HTTP 요청 전송 기능을 기본 제공한다.
- 자바스크립트를 사용하여 HTTP 요청을 전송하려면 XMLHttpRequest 객체를 사용한다.

### 43.3.1 XMLHttpRequest 객체 생성
  ```js
  // 생성자 함수를 호출하여 XMLHttpRequest 객체 생성
  const xhr = new XMLHttpRequest();
  ```

### 43.3.2 XMLHttpRequest 객체의 프로퍼티와 메서드
**프로토타입 프로퍼티**
|프로토타입 프로퍼티|설명|
|---|---|
|readyState|HTTP 요청의 현재 상태를 나타내는 정수. XMLHttpRequest의 정적 프로퍼티를 값으로 갖는다.|
|status|HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 정수 ex) 200|
|statusText|HTTP 요청에 대한 응답 메시지를 나타내는 문자열 ex) "OK"|
|responseType|HTTP 응답 타입 ex) document, json, text, blob, arraybuffer|
|response|HTTP 요청에 대한 응답 몸체. responseType에 따라 타입이 다름|
|responseText|서버가 전송한 HTTP 요청에 대한 응답 문자열|

**이벤트 핸들러 프로퍼티**
|이벤트 핸들러 프로퍼티|설명|
|---|---|
|onreadystatechange|readyState 프로퍼티 값이 변경된 경우|
|onloadstart|HTTP 요청에 대한 응답을 받기 시작한 경우|
|onprogress|HTTP 요청에 대한 응답을 받는 도중 주기적으로 발생|
|onabort|abort 메서드에 의해 HTTP 요청이 중단된 경우|
|onerror|HTTP 요청에 에러가 발생한 경우|
|onload|HTTP 요청이 성공적으로 완료한 경우|
|ontimeout|HTTP 요청 시간이 초과한 경우|
|onloadend|HTTP 요청이 완료한 경우. HTTP 요청이 성공 또는 실패하면 발생|

**메서드**
|메서드|설명|
|---|---|
|open|HTTP 요청 초기화|
|send|HTTP 요청 전송|
|abort|이미 전송된 HTTP 요청 중단|
|setRequestHeader|특정 HTTP 요청 헤더의 값을 설정|
|getResponseHeader|특정 HTTP 요청 헤더의 값을 문자열로 반환|

**정적 프로퍼티**
|정적 프로퍼티|값|설명|
|---|---|---|
|UNSENT|0|open 메서드 호출 이전|
|OPENED|1|open 메서드 호출 이후|
|HEADERS_RECEIVED|2|send 메서드 호출 이후|
|LOADING|3|서버 응답 중(응답 데이터 미완성 상태)|
|DONE|4|서버 응답 완료|

### 43.3.3 HTTP 요청 전송
HTTP 요청을 전송하는 경우 다음 순서를 따른다.
1. XMLHttpRequest.prototype.open 메서드로 HTTP 요청을 초기화한다.
2. 필요에 따라 XMLHttpRequest. prototype. setRequestHeader 메서드로 특정 HTTP 요청의 헤더 값을 설정한다.
3. XMLHttpRequest.prototype.send 메서드로 HTTP 요청을 전송한다.
    ```js
    // XMLHttpRequest 객체 생성
    const xhr = new XMLHttpRequest();

    // 1. HTTP 요청 초기화
    xhr.open('GET', '/users');

    // 2. HTTP 요청 헤더 설정
    xhr.setRequestHeader('content-type', 'application/json');

    // 3. HTTP 요청 전송
    xhr.send();
    ```

**XMLHttpRequest.prototype.open**
- open 메서드는 서버에 전송할 HTTP 요청을 초기화한다.
  ```js
  xhr.open(method, url[, async])
  ```

  |매개변수|설명|
  |---|---|
  |method|HTTP 요청 메서드("GET", "POST", "PUT", "DELETE" 등)|
  |url|HTTP 요청을 전송할 URL|
  |async|비동기 요청 여부. 옵션으로 기본값은 true이며, 비동기 방식으로 동작한다.|
- HTTP 요청 메서드는 클라이언트가 서버에게 요청 종류와 목적을 알리는 방법이다. 주로 5가지 요청 메서드(GET, POST, PUT, PATCH, DELETE)를 사용하여 CRUD를 구현한다.

**XMLHttpRequest.prototype.send**
- send 메서드는 open 메서드로 초기화된 HTTP 요청을 서버에 전송한다.
- GET, POST 요청에 따라 전송 방식에 차이가 있다.
  - GET 요청 메서드: 데이터를 URL의 일부분인 쿼리 문자열로 서버에 전송
  - POST 요청 메서드: 데이터(페이로드)를 요청 몸체에 담아 전송. 페이로드가 객체인 경우 반드시 JSON.stringify 메서드를 사용하여 직렬화한 다음 전달
    ```js
    xhr.send(JSON.stringify({id: 1, content: 'HTML', completed: false}));
    ```
- HTTP 요청 메서드가 GET인 경우 send 메서드에 페이로드로 전달한 인수는 무시되고 요청 몸체는 null로 설정된다.

**XMLHttpRequest.prototype.setRequestHeader**
- setRequestHeader 메서드는 특정 HTTP 요청의 헤더 값을 설정한다.
- setRequestHeader 메서드는 반드시 open 메서드를 호출한 이후에 호출해야 한다.
- Content-type은 요청 몸체에 담아 서버로 **전송할 데이터**의 MIME 타입의 정보를 표현한다.

  |MIME 타입|서브타입|
  |---|---|
  |text|text/plain, text/html, text/css, text/javascript|
  |application|application/json, application/x-www-form-urlencode|
  |multipart|multipart/formed-data|

  ```js
  xhr.setRequestHeader('content-type', 'application/json');
  ```

- HTTP 클라이언트가 서버에 요청할 때 서버가 **응답할 데이터**의 MIME 타입을 Accept로 지정할 수 있다.
  ```js
  xhr.setRequestHeader('accept', 'application/json');
  ```
- 만약 Accept 헤더를 설정하지 않으면 send 메서드가 호출될 때 Accept 헤더가 */*으로 전송된다.

### 43.3.4 HTTP 응답 처리
- 서버가 전송한 응답을 처리하려면 XMLHttpRequest 객체가 발생시키는 이벤트를 캐치해야 한다.
- HTTP 요청의 현재 상태를 나타내는 readyState 프로퍼티 값이 변경된 경우 발생하는 readystatechange 이벤트를 캐치하여 다음과 같이 HTTP 응답을 처리할 수 있다.
  ```js
  // XMLHttpRequest 객체 생성
  const xhr = new XMLHttpRequest();

  // HTTP 요청 초기화
  xhr.open('GET', 'https://jsonplaceholder.typicode.com/todos/1');

  // HTTP 요청 전송
  xhr.send();

  xhr.onreadystatechange = () => {
    // 서버 응답이 아직 완료되지 않았다면 아무런 처리를 하지 않는다.
    if (xhr.readyState !== XMLHttpRequest.DONE) return;

    if (xhr.state === 200) {
      console.log(JSON.parse(xhr.response));
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
  ```
- readystatechange 이벤트 대신 load 이벤트를 캐치해도 된다.
- load 이벤트는 HTTP 요청이 성공적으로 완료된 경우 발생하기 때문에 xhr.readyState가 XMLHttpRequest.DONE 인지 확인할 필요가 없다.
  ```js
  xhr.onload = () => {
    if (xhr.state === 200) {
      console.log(JSON.parse(xhr.response));
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
  ```

