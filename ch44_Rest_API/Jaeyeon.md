##  REST API



REST(Representational State Transfer)는 HTTP/1.0과 1.1의 스펙 작성에 참여했으며, 아파치 HTTP 서버 프로젝트를 공동 설립했던 로이 필딩(Roy Fielding)이 2000년 논문에서 처음 소개한 개념이다. 현재는 웹의 핵심 아키텍처로 자리잡았다.

HTTP를 충분히 활용하지 못하던 기존의 한계를 극복하기 위해, HTTP의 장점을 최대한 살릴 수 있는 아키텍처로서 REST가 제안되었다. REST의 기본 원칙을 성실히 지킨 네트워크 아키텍처는 ‘RESTful’하다고 표현한다.

즉, REST는 HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정하는 아키텍처 스타일이며, REST API는 REST 원칙을 기반으로 만든 서비스를 의미한다.

---

### 44.1 REST API의 구성

REST API는 자원(resource), 행위(verb), 표현(representation)의 3가지 요소로 구성된다. REST는 자체 표현 구조(self-descriptive message)로 구성되므로 REST API도 HTTP 요청의 내용을 명확히 나타낼 수 있다.

| 구성 요소              | 내용         | 표현 방식       |
| ------------------ | ---------- | ----------- |
| 자원(resource)       | URI로 식별    | /users      |
| 행위(verb)           | 자원에 대한 행위  | HTTP 요청 메서드 |
| 표현(representation) | 자원의 상태를 전달 | 페이로드        |

---

### 44.2 REST API 설계 원칙

REST에서 가장 중요한 기본 원칙은 두 가지다.

1. **URI는 리소스를 표현해야 한다.**

   * URI는 오직 리소스를 나타내야 하며, 행위(메서드명 등)는 URI에 포함하지 않아야 한다.
   * 예시

     ```bash
     # bad
     GET /getTodos/1
     GET /todos/show/1

     # good
     GET /todos/1
     ```

2. **리소스에 대한 행위는 HTTP 요청 메서드로 표현한다.**

   * HTTP 메서드는 리소스에 대한 행동(CRUD)을 나타낸다.

   * 주요 메서드

     | HTTP 요청 메서드 | 종류             | 목적     | 페이로드 |
     | ----------- | -------------- | ------ | ---- |
     | GET         | index/retrieve | 리소스 조회 | X    |
     | POST        | create         | 리소스 생성 | O    |
     | PUT         | replace        | 전체 교체  | O    |
     | PATCH       | modify         | 일부 수정  | O    |
     | DELETE      | delete         | 리소스 삭제 | X    |

   * 예시

     ```bash
     # bad
     GET /todos/delete/1

     # good
     DELETE /todos/1
     ```

---

## 44.3 JSON Server를 이용한 REST API 실습

HTTP 요청을 전송하고 응답을 받기 위해, JSON Server로 간단하게 REST API 서버를 구축할 수 있다.

---

### 44.3.1 JSON Server 설치

```bash
$ mkdir json-server-exam && cd json-server-exam
$ npm init -y
$ npm install json-server --save-dev
```

---

### 44.3.2 db.json 파일 생성

`json-server-exam` 폴더에 아래와 같이 `db.json` 파일을 생성한다.

```json
{
  "todos": [
    {
      "id": 1,
      "content": "HTML",
      "completed": true
    },
    {
      "id": 2,
      "content": "CSS",
      "completed": false
    },
    {
      "id": 3,
      "content": "JavaScript",
      "completed": true
    }
  ]
}
```

---

### 44.3.3 JSON Server 실행

```bash
$ json-server --watch db.json
```

포트를 바꾸려면:

```bash
$ json-server --watch db.json --port 5000
```

`package.json`에 등록해서 npm으로도 실행할 수 있다.

```json
{
  "name": "json-server-exam",
  "version": "1.0.0",
  "scripts": {
    "start": "json-server --watch db.json"
  },
  "devDependencies": {
    "json-server": "^0.16.1"
  }
}
```

```bash
$ npm start
```

---

### 44.3.4 GET 요청 (index)

모든 todo를 조회하는 예시

```html
<!DOCTYPE html>
<html>
  <body>
    <pre></pre>
    <script>
      const xhr = new XMLHttpRequest();
      xhr.open('GET', '/todos');
      xhr.send();
      xhr.onload = () => {
        if (xhr.status === 200) {
          document.querySelector('pre').textContent = xhr.response;
        } else {
          console.error('Error', xhr.status, xhr.statusText);
        }
      };
    </script>
  </body>
</html>
```

---

### 44.3.4 GET 요청 (retrieve)

특정 todo만 조회할 때

```html
<!DOCTYPE html>
<html>
<body>
<pre></pre>
<script>
  const xhr = new XMLHttpRequest();
  xhr.open('GET', '/todos/1');
  xhr.send();
  xhr.onload = () => {
    if (xhr.status === 200) {
      document.querySelector('pre').textContent = xhr.response;
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
</script>
</body>
</html>
```

---

### 44.3.5 POST 요청 (create)

새 todo 추가

```html
<!DOCTYPE html>
<html>
<body>
<pre></pre>
<script>
  const xhr = new XMLHttpRequest();
  xhr.open('POST', '/todos');
  xhr.setRequestHeader('content-type', 'application/json');
  xhr.send(JSON.stringify({ id: 4, content: 'Angular', completed: false }));
  xhr.onload = () => {
    if (xhr.status === 200 || xhr.status === 201) {
      document.querySelector('pre').textContent = xhr.response;
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
</script>
</body>
</html>
```

---

### 44.3.6 PUT 요청

특정 todo 전체를 교체

```html
<!DOCTYPE html>
<html>
<body>
<pre></pre>
<script>
  const xhr = new XMLHttpRequest();
  xhr.open('PUT', '/todos/4');
  xhr.setRequestHeader('content-type', 'application/json');
  xhr.send(JSON.stringify({ id: 4, content: 'React', completed: true }));
  xhr.onload = () => {
    if (xhr.status === 200) {
      document.querySelector('pre').textContent = xhr.response;
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
</script>
</body>
</html>
```

---

### 44.3.7 PATCH 요청

특정 todo 일부만 수정

```html
<!DOCTYPE html>
<html>
<body>
<pre></pre>
<script>
  const xhr = new XMLHttpRequest();
  xhr.open('PATCH', '/todos/4');
  xhr.setRequestHeader('content-type', 'application/json');
  xhr.send(JSON.stringify({ completed: false }));
  xhr.onload = () => {
    if (xhr.status === 200) {
      document.querySelector('pre').textContent = xhr.response;
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
</script>
</body>
</html>
```

---

### 44.3.8 DELETE 요청

특정 todo 삭제

```html
<!DOCTYPE html>
<html>
<body>
<pre></pre>
<script>
  const xhr = new XMLHttpRequest();
  xhr.open('DELETE', '/todos/4');
  xhr.send();
  xhr.onload = () => {
    if (xhr.status === 200) {
      document.querySelector('pre').textContent = xhr.response;
    } else {
      console.error('Error', xhr.status, xhr.statusText);
    }
  };
</script>
</body>
</html>
```

