## 48.1 모듈의 일반적 의미
- 모듈이란 애플리케이션을 구성하는 개별적 요소로서 재사용 가능한 코드 조각을 말한다.
- 모듈이 성립하려면 자신만의 파일 스코프(모듈 스코프)를 가질 수 있어야 하며, 자신만의 파일 스코프를 갖는 모듈의 모든 자산(모듈에 포함되어 있는 변수, 함수, 객체 등)은 캡슐화되어 다른 모듈에서 접근할 수 없다.
- 모듈은 공개가 필요한 자산에 한정하여 명시적으로 선택적 공개가 가능한데, 이를 export라 한다.
- 모듈 사용자는 모듈이 공개한 자산 중 일부 또는 전체를 선택해 자신의 스코프 내로 불러들여 재사용할 수 있다. (import)
- 모듈은 코드의 단위를 명확히 분리하여 애플리케이션을 구성할 수 있고, 재사용성이 좋아서 개발 효율성과 유지보수성을 높일 수 있다.

## 48.2 자바스크립트와 모듈
- 자바스크립트는 웹페이지의 단순한 보조 기능을 처리하기 위한 제한적인 용도를 목적으로 태어났기 때문에 모듈이 성립하기 위해 필요한 파일 스코프와 import, export를 지원하지 않았다.
- C 언어는 #include, 자바는 import 등 대부분의 프로그래밍 언어는 모듈 기능을 가지고 있다. 자바스크립트는 script 태그를 사용하여 외부의 자바스크립트 파일을 로드할 수는 있지만 파일마다 독립적인 파일 스코프를 갖지 않는다.

## 48.3 ES6모듈(ESM)
- ES6에서는 클라이언트 사이드 자바스크립트에서도 동작하는 모듈 기능을 추가했다.
- script 태그에 type="module" 어트리뷰트를 추가하면 로드된 자바스크립트 파일은 모듈로서 동작한다.
- 일반적인 자바스크립트 파일이 아닌 ESM임을 명확히 하기 위해 ESM의 파일 확장자는 mjs를 사용할 것을 권장한다.
  ```js
  <script type="module" src="app.mjs"></script>
  ```

### 48.3.1 모듈 스코프
- 일반적인 자바스크립트 파일은 script 태그로 분리해서 로드해도 독자적인 모듈 스코프를 갖지 않지만, ESM은 독자적인 모듈 스코프를 갖는다.
  ```js
  // foo.mjs
  var x = 'foo';
  console.log(x); // foo
  console.log(window.x); // undefined

  // bar.mjs
  var x = 'bar';
  console.log(x); // bar
  console.log(window.x); // undefined

  <script type="module" src="foo.mjs"></script>
  <script type="module" src="bar.mjs"></script>
  ```
- 모듈 내에서 선언한 식별자는 모듈 외부에서 참조할 수 없다. 모듈 스코프가 다르기 때문이다.
  ```js
  // foo.mjs
  var x = 'foo';
  console.log(x); // foo

  // bar.mjs
  console.log(x); // ReferenceError: x is not defined
  ```

### 48.3.2 export 키워드
- 모듈 내부에서 선언한 식별자를 외부에 공개하여 다른 모듈들이 재사용할 수 있게 하려면 export 키워드를 사용한다.
- export 키워드는 선언문 앞에 사용하므로 변수 함수, 클래스 등 모든 식별자를 export할 수 있다.
  ```js
  export const pi = Math.PI;

  export function square(x) {
    return x * x;
  }

  export class Person {
    constructor(name) {
      this.name = name;
    }
  }
  ```
- 선언문 앞에 매번 export 키워드를 붙이는 것이 번거롭다면 export할 대상을 하나의 객체로 구성하여 한 번에 export할 수도 있다.
  ```js
  const pi = Math.PI;

  function square(x) {
    return x * x;
  }

  class Person {
    constructor(name) {
      this.name = name;
    }
  }

  export { pi, square, Person };
  ```

### 48.3.3 import 키워드
- 다른 모듈에서 공개한 식별자를 자신의 모듈 스코프 내부로 로드하려면 import 키워드를 사용한다. 
- 다른 모듈이 export한 식별자 이름으로 import해야 하며 ESM의 경우 파일 확장자를 생략할 수 없다.
  ```js
  // app.mjs
  import { pi, square, Person } from './lib.mjs';

  console.log(pi); // 3.141592653589793
  console.log(square(10)); // 100
  console.log(new Person('Lee')); // Person { name: 'Lee' }


  <script type="module" src="app.mjs"></script>
  ```
- lib.mjs 모듈이 export한 모든 식별자를 lib 객체의 프로퍼티로 모아 import할 수도 있다.
  ```js
  import * as lib from './lib.mjs';

  console.log(lib.pi); 
  console.log(lib.square(10)); 
  console.log(new lib.Person('Lee')); 
  ```
- 모듈이 export한 식별자 이름을 변경하여 import할 수도 있다.
  ```js
  import { pi as PI, square as sq, Person as P } from './lib.mjs';

  console.log(PI); 
  console.log(sq(10));
  console.log(new P('Lee')); 
  ```
- 모듈에서 하나의 값만 export한다면 default 키워드를 사용할 수 있다.
- default 키워드를 사용하는 경우 var, let, const 키워드는 사용할 수 없다.
- default 키워드와 함께 export한 모듈은 {} 없이 임의의 이름으로 import한다.
  ```js
  // lib.mjs
  export default x => x * x;

  // app.mjs
  import square from './lib.mjs';

  console.log(square(3)); // 9
  ```
