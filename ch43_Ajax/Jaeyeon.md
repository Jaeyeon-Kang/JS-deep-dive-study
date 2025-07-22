## 43장

# Ajax

### 43.1 Ajax란?


> 그림 43-1 — 전통적인 웹 페이지 요청/응답 방식
<img width="520" height="506" alt="image" src="https://github.com/user-attachments/assets/27641f0e-1875-4d2c-bc05-9d9652de3dda" />


이러한 전통적인 방식은 다음과 같은 단점이 있다.

1. 이전 웹페이지와의 차이가 없어서 불러올 필요가 없는 부분까지 불필요한 중복된 HTML을 서버로부터 매번 다시 전달받음에 따라 전체적인 페이지 로드에 불필요한 데이터 통신이 발생한다.
2. 변경된 내용이 없는 부분까지 포함해서 다시 렌더링한다. 이로 인해 렌더링에 의한 플리커링(깜박임) 현상과 같은 부자연스러운 사용자 경험이 생성된다.
3. 클라이언트와 서버의 요청이 동기 방식으로 동작하기 때문에 서버로부터 응답이 오기 전까지는 클라이언트는 응답을 기다려야 한다.

- Ajax의 등장은 이러한 전통적인 웹페이지를 획기적으로 바꿔 놓았다. Ajax를 사용하면 웹페이지 전체를 다시 로드하지 않고도 필요한 데이터만을 비동기 방식으로 서버로부터 전달받아 웹페이지의 일부분만을 갱신할 수 있다.
- 불필요한 데이터 통신과 렌더링을 줄일 수 있어 사용자 경험이 향상 + 네트워크 트래픽과 서버의 부하도 줄이게 됨



> 그림 43-2 — Ajax

<img width="532" height="434" alt="image" src="https://github.com/user-attachments/assets/b7a444cc-6903-4bdf-b0ab-c0725aad0aa6" />



Ajax 장점

1. 전체 페이지를 다시 로드할 필요가 없으므로 서버로부터 필요한 데이터만 전달받아 불필요한 데이터 통신이 발생하지 않는다.
2. 변경된 부분만을 선택적으로 갱신하므로 플리커링과 같은 부자연스러운 현상이 발생하지 않는다.
3. 클라이언트는 서버로부터 응답을 받기 전까지 기다리지 않고 비동기적으로 서버와 통신함으로써 응답이 없는 동안에도 다른 작업을 수행할 수 있다.



### 43.2 JSON

JSON은 클라이언트와 서버 간의 HTTP 통신 시 데이터를 텍스트 포맷의 구조화된 문자열로 교환하기 위한 데이터 포맷으로로 대부분의 프로그래밍 언어에서 사용할 수 있다.



#### 43.2.1 JSON 표기 방식

JSON은 자바스크립트의 객체 리터럴과 유사하게 key와 value로 구성된 순수한 텍스트다.

```js
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```


#### 43.2.2 JSON.stringify

JSON.stringify 메서드는 객체를 JSON 포맷의 문자열로 변환한다. 클라이언트가 서버로 데이터를 전송할 때 객체를 문자열화해야 하는데 이를 직렬화(serialization)라 한다.

```js
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis']
};

// 객체를 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(obj);
console.log(typeof json, json);
// string '{"name":"Lee","age":20,"alive":true,"hobby":["traveling","tennis"]}'

// 객체를 JSON 포맷의 문자열로 보기 좋게 변환한다.
const prettyJson = JSON.stringify(obj, null, 2);
console.log(typeof prettyJson, prettyJson);
/*
string '{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": [
    "traveling",
    "tennis"
  ]
}'
*/
```

JSON.stringify 메서드는 객체뿐만 아니라 배열도 JSON 포맷의 문자열로 변환한다.

> \[예제 43-03]

```js
const todos = [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'JavaScript', completed: false }
];

// 배열을 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(todos, null, 2);
console.log(typeof json, json);
/*
string
[
  {
    "id": 1,
    "content": "HTML",
    "completed": false
  },
  {
    "id": 2,
    "content": "CSS",
    "completed": true
  },
  {
    "id": 3,
    "content": "JavaScript",
    "completed": false
  }
]
*/
```



### 43.2.3 JSON.parse

JSON.parse 메서드는 JSON 포맷의 문자열을 객체로 변환한다. 서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열이다. 이 문자열을 객체로서 사용하려면 JSON 포맷의 문자열을 객체로 변환해야 하는데 이때 사용하는 메서드가 JSON.parse다.

> \[예제 43-04]

```js
const obj = {
  name: 'Lee',
  age: 20,
  alive: true,
  hobby: ['traveling', 'tennis']
};

// 객체를 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(obj);

// JSON 포맷의 문자열을 객체로 변환한다.
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
// object {name: 'Lee', age: 20, alive: true, hobby: ['traveling', 'tennis']}
```

배열인 JSON 포맷의 문자열로 변환되어 있는 경우 JSON.parse는 문자열을 배열 객체로 변환한다. 배열의 요소가 객체일 경우 배열의 요소까지 객체로 변환된다.

> \[예제 43-05]

```js
const todos = [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'JavaScript', completed: false }
];

// 배열을 JSON 포맷의 문자열로 변환한다.
const json = JSON.stringify(todos);

// JSON 포맷의 문자열을 배열 객체로 변환한다.
const parsed = JSON.parse(json);
console.log(typeof parsed, parsed);
/*
object [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 3, content: 'JavaScript', completed: false }
]
*/
```



### 43.3 XMLHttpRequest

* 브라우저는 기본적으로 주소창, form, 링크 등을 통해 페이지 요청과 응답을 처리한다.
* 자바스크립트에서 직접 HTTP 요청을 보내고 싶을 때는 XMLHttpRequest 객체를 사용한다.
* XMLHttpRequest는 요청 전송, 응답 수신, 상태 확인, 이벤트 처리 등 다양한 기능을 제공한다.


#### 43.3.1 XMLHttpRequest 객체 생성

XMLHttpRequest 객체는 XMLHttpRequest 생성자 함수를 호출하여 생성한다. XMLHttpRequest 생성자 함수는 브라우저의 Web API로 제공되며 XMLHttpRequest 생성자 함수는 전역 객체 window에 포함되어 있다.

> \[예제 43-06]

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();
```



#### 43.3.2 XMLHttpRequest 객체의 프로퍼티와 메서드

대표적인 프로퍼티와 메서드는 다음과 같다.



**XMLHttpRequest 객체의 프로토타입 프로퍼티**

| 프로토타입 프로퍼티 | 설명                                                                                                                                                   |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| readyState | HTTP 요청의 현재 상태를 나타내는 값. 다음과 같은 XMLHttpRequest의 정적 프로퍼티 값 중 하나다.<br>- UNSENT: 0<br>- OPENED: 1<br>- HEADERS\_RECEIVED: 2<br>- LOADING: 3<br>- DONE: 4 |
| status     | HTTP 요청에 대한 응답 상태(HTTP 상태 코드)를 나타내는 값<br>예: 200                                                                                                      |



**XMLHttpRequest 객체의 정적 프로퍼티**

| 정적 프로퍼티 | 값 | 설명             |
| ------- | - | -------------- |
| UNSENT  | 0 | open 메서드 호출 이전 |
| OPENED  | 1 | open 메서드 호출 이후 |



**XMLHttpRequest 객체의 프로퍼티**

| 프로퍼티명        | 설명                                                   |
| ------------ | ---------------------------------------------------- |
| statusText   | HTTP 응답에 대한 응답 메시지를 나타내는 문자열 예: “OK”                 |
| responseType | 응답 타입 예: document, json, text, blob, arraybuffer     |
| response     | HTTP 요청에 대한 응답(body 부분). responseType에 따라 데이터 타입이 다름 |
| responseText | 응답 body를 문자열로 반환. responseType이 text인 경우에만 유효        |



**XMLHttpRequest 객체의 이벤트 핸들러 프로퍼티**

| 이벤트 핸들러 프로퍼티       | 설명                                          |
| ------------------ | ------------------------------------------- |
| onreadystatechange | readyState 프로퍼티 값이 변경될 경우                   |
| onloadstart        | HTTP 요청이 완료되기 전에 호출되는 핸들러                   |
| onprogress         | HTTP 요청에 대한 응답을 수신 중일 때 주기적으로 호출됨           |
| onabort            | abort 메서드에 의해 HTTP 요청이 중단되었을 때              |
| onerror            | HTTP 요청에 실패했을 때                             |
| onload             | HTTP 요청이 성공적으로 완료되었을 때                      |
| ontimeout          | HTTP 요청이 타임아웃될 경우                           |
| onloadend          | HTTP 요청이 완료된 경우, HTTP 요청 성공 또는 실패와 관계없이 호출됨 |



**XMLHttpRequest 객체의 메서드**

| 메서드               | 설명                 |
| ----------------- | ------------------ |
| open              | HTTP 요청 초기화        |
| send              | HTTP 요청 전송         |
| abort             | 이미 전송된 HTTP 요청을 중단 |
| setRequestHeader  | HTTP 요청 헤더를 설정     |
| getResponseHeader | 특정 응답 헤더 필드를 반환    |



#### 정적 프로퍼티 

| 정적 프로퍼티           | 값 | 설명                     |
| ----------------- | - | ---------------------- |
|  UNSENT           | 0  | open 메서드 호출 이전 |
|  OPENED           | 1  | open 메서드 호출 이후  |
| HEADERS\_RECEIVED | 2 | send 메서드 호출 이후         |
| LOADING           | 3 | 서버 응답 중 (일부 데이터 수신 상태) |
| DONE              | 4 | 서버 응답 완료               |

### 43.3.3 HTTP 요청 전송

HTTP 요청을 전송하는 경우 다음 순서를 따른다.

1. `XMLHttpRequest.prototype.open` 메서드로 HTTP 요청을 초기화한다.
2. 필요에 따라 `XMLHttpRequest.prototype.setRequestHeader` 메서드로 클라이언트가 서버로 전송할 HTTP 요청 헤더의 값을 설정한다.
3. `XMLHttpRequest.prototype.send` 메서드로 HTTP 요청을 전송한다.

> \[예제 43-07]

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
xhr.open('GET', '/users');

// HTTP 요청 헤더 설정 (전송할 데이터의 MIME 타입 지정: json)
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send();
```



#### XMLHttpRequest.prototype.open

open 메서드는 서버에 전송할 HTTP 요청을 초기화한다. open 메서드를 호출하는 방법은 다음과 같다.

```js
xhr.open(method, url[, async])
```

| 매개변수   | 설명                                             |
| ------ | ---------------------------------------------- |
| method | HTTP 요청 메서드 ("GET", "POST", "PUT", "DELETE" 등) |
| url    | HTTP 요청을 전송할 URL                               |
| async  | 비동기 여부 설정. 옵션이며 기본값은 `true`이다. `false`는 동기 요청  |



HTTP 요청 메서드는 클라이언트가 서버에 요청의 목적을 나타내기 위해 사용하는 방법이다.
주로 5가지 요청 메서드 (GET, POST, PUT, PATCH, DELETE)를 사용하여 CRUD를 구현한다.

| HTTP 요청 메서드 | 종류             | 용도(기능) 설명    | 페이로드 |
| ----------- | -------------- | ------------ | ---- |
| GET         | index/retrieve | 모든/특정 리소스 조회 | X    |
| POST        | create         | 리소스 생성       | O    |
| PUT         | replace        | 리소스 전체 교체    | O    |
| PATCH       | modify         | 리소스 일부 수정    | O    |
| DELETE      | delete         | 리소스 삭제/특정 삭제 | X    |



#### XMLHttpRequest.prototype.send

send 메서드는 open 메서드로 초기화된 HTTP 요청을 서버에 전송한다. 기본적으로 서버로 전송하는 데이터는 `GET`, `POST`, `요청 메서드`에 따라 전송 방식이 달라진다.

* `GET` 요청 메서드의 경우 데이터(페이로드)를 URL의 쿼리 스트링에 담아 전송
* `POST` 요청 메서드의 경우 요청 몸체(Body)에 담아 전송

<img width="583" height="476" alt="image" src="https://github.com/user-attachments/assets/35fcc23e-f615-44ec-8d03-59f1cc6ba244" />



send 메서드는 요청 몸체에 담아 전송할 데이터를(페이로드)를 인수로 전달할 수 있다. 페이로드가 객체인 경우 반드시 JSON.stringify 메서드를 사용하여 문자열로 변환해야 한다.

> \[예제 43-08]

```js
xhr.send(JSON.stringify({ id: 1, content: 'HTML', completed: false }));
```



HTTP 요청 메서드가 GET인 경우 send 메서드로 전달한 인수는 무시되고 요청 본문은 전송되지 않는다.



#### XMLHttpRequest.prototype.setRequestHeader

`setRequestHeader` 메서드는 클라이언트가 서버로 전송할 HTTP 요청 헤더의 값을 설정한다. 자주 사용하는 HTTP 요청 헤더인 `Content-type`을 설정해보자.

`Content-type`은 요청 몸체에 담아 전송하는 데이터의 MIME 타입을 지정하는데 사용된다. 자주 사용하는 MIME 타입은 다음과 같다.

| MIME 타입     | 서브타입                                                |
| ----------- | --------------------------------------------------- |
| text        | text/plain, text/html, text/css, text/javascript    |
| application | application/json, application/x-www-form-urlencoded |
| multipart   | multipart/form-data                                 |

다음은 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정하는 예다.

> \[예제 43-09]

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
xhr.open('POST', '/users');

// HTTP 요청 헤더 설정 (전송할 데이터 MIME 타입 지정: json)
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send(JSON.stringify({ id: 1, content: 'HTML', completed: false }));
```



HTTP 클라이언트가 서버에 요청할 때 서버가 응답할 데이터의 MIME 타입을 `Accept`로 지정할 수 있다.
다음은 서버가 응답할 데이터의 MIME 타입을 지정하는 예다.

> \[예제 43-10]

```js
// 서버가 응답할 데이터 MIME 타입 지정: json
xhr.setRequestHeader('Accept', 'application/json');
```

※ Accept 헤더를 설정하지 않으면 요청 헤더에 `Accept: */*`이 자동으로 전송된다.


### 43.3.4 HTTP 응답 처리

* 서버로부터 HTTP 응답을 받을 때는 **XMLHttpRequest 객체의 이벤트 핸들러**를 이용해 응답을 처리한다.

* 가장 자주 사용하는 방식은 **onreadystatechange** 이벤트 핸들러를 등록하는 것인데, 이 핸들러는 요청의 상태가 바뀔 때마다 호출된다.

* 예제 43-11처럼,

  ```js
  xhr.onreadystatechange = () => {
    if (xhr.readyState !== XMLHttpRequest.DONE) return;  // 응답이 완료될 때까지 대기
    if (xhr.status === 200) {  // 응답이 정상적으로 도착했을 때
      console.log(JSON.parse(xhr.response));  // 응답 데이터를 객체로 변환해 사용
    } else {
      console.error('Error', xhr.status, xhr.statusText);  // 에러 처리
    }
  }
  ```

  이 구조로 콜백을 작성하면, 요청이 끝나고 응답이 왔을 때(readyState가 DONE이고 status가 200인 경우)만 데이터를 처리한다.

* 만약 **응답 데이터가 JSON 문자열**이라면, 반드시 `JSON.parse(xhr.response)`로 변환해서 객체처럼 사용해야 한다.

* onreadystatechange 내부에서는 아래처럼 간단히 사용할 수도 있다.

  ```js
  if (xhr.readyState === XMLHttpRequest.DONE && xhr.status === 200) {
    console.log(JSON.parse(xhr.response));
  }
  ```

* 이 방식 대신 더 간단하게 **onload 이벤트**를 사용할 수도 있다.
  예제 43-12처럼,

  ```js
  xhr.onload = () => {
    if (xhr.status === 200) {
      console.log(JSON.parse(xhr.response));
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
  ```

  이렇게 쓰면 응답이 성공적으로 완료됐을 때만 실행되기 때문에, readyState 값을 따로 검사하지 않아도 된다.

* 즉, 두 예제 모두 서버 응답을 받아 JSON 데이터를 객체로 변환하고, 정상일 때만 처리하는 구조다. onreadystatechange는 상태를 직접 체크해야 하고, onload는 자동으로 완료 시점에만 실행된다는 차이가 있다.
