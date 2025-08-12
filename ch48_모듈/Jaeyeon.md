
## 48.1 모듈의 일반적 의미

- **모듈(module)**이란 애플리케이션을 구성하는 개별적 요소로서 재사용 가능한 코드 조각을 말한다.
- 일반적으로 모듈은 기능을 기준으로 **파일 단위**로 분리한다.
- 모듈이 성립하려면 모듈은 자신만의 **파일 스코프(모듈 스코프)**를 가질 수 있어야 한다.
- 자신만의 파일 스코프를 갖는 모듈의 자산(변수, 함수, 객체 등)은 기본적으로 **캡슐화**되어 비공개 상태이며, 다른 모듈에서 접근할 수 없다.
- 모듈은 공개가 필요한 자산에 한정하여 명시적으로 선택적 공개가 가능하다. 이를 **`export`**라 한다.
- 모듈 사용자는 모듈이 공개(`export`)한 자산 중 일부 또는 전체를 선택해 자신의 스코프 내로 불러들여 재사용할 수 있다. 이를 **`import`**라 한다.

---

## 48.2 자바스크립트와 모듈

- 자바스크립트는 원래 모듈 시스템을 지원하지 않았다.
- 자바스크립트 파일을 여러 개의 파일로 분리하여 `<script>` 태그로 로드해도, 결국 하나의 자바스크립트 파일 내에 있는 것처럼 동작한다.
- 즉, 모든 자바스크립트 파일은 하나의 전역을 공유하므로, 전역 변수 중복 등의 문제가 발생할 수 있다.
- 자바스크립트 모듈 시스템은 크게 **CommonJS**와 **AMD** 진영으로 나뉘게 되었다.
- Node.js는 모듈 시스템의 사실상 표준인 CommonJS를 채택했고, ECMAScript 표준 사양은 아니지만 모듈 시스템을 지원한다.
- 따라서 Node.js 환경에서는 파일별로 독립적인 파일 스코프(모듈 스코프)를 갖는다.

---

## 48.3 ES6 모듈(ESM)

- ES6에서는 클라이언트 사이드 자바스크립트에서도 동작하는 모듈 기능을 추가했다.
- IE를 제외한 대부분의 브라우저에서 ES6 모듈을 사용할 수 있다.
- ES6 모듈(ESM)의 사용법은 간단하다. `<script>` 태그에 `type="module"` 속성을 추가하면 로드된 파일은 모듈로서 동작한다.
- 일반적인 자바스크립트 파일이 아닌 ESM임을 명확히 하기 위해 파일 확장자로 `.mjs`를 사용할 것을 권장한다.

```html
<script type="module" src="app.mjs"></script>
````

---

### 48.3.1 모듈 스코프

* ESM은 독자적인 모듈 스코프를 갖는다.
* 일반적인 자바스크립트 파일은 `<script>` 태그로 분리해도 독자적인 모듈 스코프를 갖지 않는다.

---

### 48.3.2 `export` 키워드

* `export` 키워드는 선언문 앞에 붙여 사용한다.
* 변수, 함수, 클래스 등 모든 식별자를 `export`할 수 있다.

```javascript
// lib.mjs

// 변수의 공개
export const pi = Math.PI;

// 함수의 공개
export function square(x) {
    return x * x;
}

// 클래스의 공개
export class Person {
    constructor(name) {
        this.name = name;
    }
}

// 여러 개를 한 번에 export
export { pi, square, Person };
```

---

### 48.3.3 `import` 키워드

* 다른 모듈에서 공개(`export`)한 식별자를 자신의 모듈 스코프 내부로 로드하려면 `import` 키워드를 사용한다.
* 다른 모듈이 `export`한 식별자 이름으로 `import`해야 하며, ESM의 경우 파일 확장자를 생략할 수 없다.

```javascript
// app.mjs
import { pi, square, Person } from './lib.mjs';

console.log(pi); // 3.141592653589793
console.log(square(10)); // 100
console.log(new Person('Lee')); // Person { name: 'Lee' }
```

```html
<!DOCTYPE html>
<html>
<body>
    <script type="module" src="app.mjs"></script>
</body>
</html>
```

* `app.mjs`는 애플리케이션의 진입점이므로 반드시 `<script>` 태그로 로드해야 한다.
* `lib.mjs`는 `app.mjs`의 `import` 문에 의해 로드되는 **의존성**이므로, 별도의 `<script>` 태그로 로드할 필요가 없다.

---

#### 추가적인 `import` 방식

* 모듈이 `export`한 식별자를 하나의 이름에 한 번에 `import`할 수 있다.
  이 경우, `as` 뒤에 지정한 이름의 객체에 프로퍼티로 할당된다.

```javascript
import * as lib from './lib.mjs';
console.log(lib.pi);
console.log(lib.square(5));
```

* 모듈이 `export`한 식별자 이름을 변경하여 `import`할 수도 있다.

```javascript
import { pi as PI, square as sq } from './lib.mjs';
```

* 모듈에서 하나의 값만 `export`한다면 `default` 키워드를 사용할 수 있다.
  이 경우 `var`, `let`, `const` 키워드는 사용할 수 없다.

```javascript
export default x => x * x;
```

* `default` 키워드로 `export`한 모듈은 `{}` 없이 임의의 이름으로 `import`한다.

```javascript
import square from './lib.mjs';
```


