
## 49. ES6+/ES.NEXT 개발 환경 구축

대부분의 최신 브라우저의 ES6 지원율은 높지만, **IE 11**의 ES6 지원율은 낮기 때문에  
IE를 포함한 구형 브라우저에서 문제 없이 동작시키기 위한 개발 환경을 구축해야 한다.  
이를 위해 **트랜스파일러 Babel**과 **모듈 번들러 Webpack**을 사용한다.

ES6 모듈은 대부분의 모던 브라우저에서 사용할 수 있지만, 다음과 같은 이유로 별도의 모듈 로더를 사용하는 것이 일반적이다.

- IE를 포함한 구형 브라우저는 ESM을 지원하지 않는다.
- ESM을 사용하더라도 트랜스파일링이나 번들링이 필요한 것은 변함이 없다.
- ESM이 아직 지원하지 않는 기능이 있으며, 점차 해결되고 있지만 일부 이슈가 존재한다.

---

## 49.1 Babel

Babel은 **ES6+/ES.NEXT**로 구현된 최신 사양의 소스 코드를 IE와 같은 구형 브라우저에서도 동작 가능한 **ES5** 사양 코드로 변환해 준다.

### 49.1.1 Babel 설치

```bash
npm init -y
npm install --save-dev @babel/core @babel/cli
````

---

### 49.1.2 Babel 프리셋 설치와 설정

Babel을 사용하기 위해서는 **@babel/preset-env**를 설치해야 한다.
이는 프로젝트 지원 환경에 맞춰 필요한 플러그인을 동적으로 결정해 준다.
지원 환경은 **.browserlistrc** 파일에 Browserlist 형식으로 설정할 수 있다.

```bash
npm install --save-dev @babel/preset-env
```

설치 후 프로젝트 루트에 `babel.config.json` 생성:

```json
{
  "presets": ["@babel/preset-env"]
}
```

---

### 49.1.3 트랜스파일링

`package.json`의 scripts에 명령어 등록:

```json
"scripts": {
  "build": "babel src/js -w -d dist/js"
}
```

* `-w`: 변경 감지 후 자동 트랜스파일 (`--watch`)
* `-d`: 출력 폴더 지정 (`--out-dir`)

```bash
npm run build
```

단, ES6 클래스의 `#private` 필드 등 일부 제안 기능은 별도의 플러그인 설치가 필요하다.

---

### 49.1.4 Babel 플러그인 설치

```bash
npm install --save-dev @babel/plugin-proposal-class-properties
```

`babel.config.json` 수정:

```json
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

---

### 49.1.5 브라우저에서 모듈 로딩 테스트

* Node.js 환경에서는 CommonJS의 `require`가 동작하지만, 브라우저는 지원하지 않는다.
* 브라우저에서 ESM을 사용할 수 있으나, 앞서 언급한 제약이 있으므로 Webpack을 사용한다.

---

## 49.2 Webpack

Webpack은 JS, CSS, 이미지 등의 의존 관계 리소스를 하나로 **번들링**하는 모듈 번들러다.

* 의존 모듈이 하나의 파일로 묶여 별도의 모듈 로더 불필요
* HTML에서 여러 개의 `<script>` 태그를 작성할 필요가 없음
* Babel과 함께 사용하여 ES6+/ES.NEXT → ES5 변환 가능

---

### 49.2.1 Webpack 설치

```bash
npm install --save-dev webpack webpack-cli
```

`package.json` 예시:

```json
{
  "name": "esnext-project",
  "version": "1.0.0",
  "scripts": {
    "build": "babel src/js -w -d dist/js"
  },
  "devDependencies": {
    "@babel/cli": "^7.10.3",
    "@babel/core": "^7.10.3",
    "@babel/plugin-proposal-class-properties": "^7.10.1",
    "@babel/preset-env": "^7.10.3",
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.12"
  }
}
```

---

### 49.2.2 babel-loader 설치

```bash
npm install --save-dev babel-loader
```

Webpack이 모듈을 번들링할 때 Babel로 트랜스파일하도록 설정.
`package.json` 수정:

```json
"scripts": {
  "build": "webpack -w"
}
```

---

### 49.2.3 webpack.config.js 작성

```javascript
const path = require("path");

module.exports = {
  entry: "./src/js/main.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "js/bundle.js",
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        include: [path.resolve(__dirname, "src/js")],
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"],
            plugins: ["@babel/plugin-proposal-class-properties"],
          },
        },
      },
    ],
  },
  devtool: "source-map",
  mode: "development",
};
```

실행 후 `dist/js/bundle.js`가 생성되며, 모든 모듈이 하나로 번들링된다.

```html
<!DOCTYPE html>
<html>
<body>
  <script src="dist/js/bundle.js"></script>
</body>
</html>
```

---

### 49.2.4 babel-polyfill 설치

Babel로 변환해도 **Promise**, **Object.assign**, **Array.from** 등
ES5에 대체 기능이 없는 경우는 트랜스파일되지 않는다. 이를 위해 폴리필 설치:

```bash
npm install @babel/polyfill
```

`package.json`:

```json
"dependencies": {
  "@babel/polyfill": "^7.10.1"
}
```

---

#### 사용 방법

1. **import 방식**

```javascript
import "@babel/polyfill";
import { pi, power, Foo } from "./lib";
```

2. **webpack.config.js entry에 추가**

```javascript
entry: ["@babel/polyfill", "./src/js/main.js"],
```

Webpack 실행 후 `bundle.js`에 폴리필이 포함된 것을 확인할 수 있다.

