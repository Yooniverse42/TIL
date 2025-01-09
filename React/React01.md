# Node.js 기초

## 1. Node.js에 대해 알아보자

### Node.js가 왜 필요한가?

React.js는 Node.js를 기반으로 동작하는 기술임!

### Node.js란?

자바스크립트 실행 환경(Run Time) = 구동기  
⇒ 왜 만들었는가? 자바스크립트는 웹 페이지 내부에 필요한 아주 단순한 기능만을 개발하기 위해 만들어짐.  
즉, 매우 유연하고 작성하기 쉽도록 언어가 설계되어있음. 생산성이 매우 높음. 그래서 웹 브라우저 밖에서도 쓰고 싶었던 것!  
그래서 Node.js가 나오면서 어디서든 동작이 되는 범용적인 언어가 됨!

<br />

## 2. Node.js 사용하기

```bash
npm init
```

⇒ pakage.json 파일이 생성될 것임.

js파일에서 `consol.log('hi');` 작성 후 터미널에서 아래와 같은 명령어를 사용하면?

```bash
node index.js
# node (실행시키고 싶은 파일)
```

“hi”가 출력이 될 것임

package.json에 scripts 추가하면 터미널에서 쉽게 사용 가능

```json
{
  "scripts": {
    "start": "node src/index.js"
  }
}
```

```bash
npm run start
# => hi 출력
```

<br />

## 3. Node.js 모듈 시스템 이해하기

**모듈(Module)** : 기능별 나누어진 파일들  
**모듈 시스템** : 모듈을 생성하고, 불러오고, 사용하는 등의 모듈을 다루는 다양한 기능을 제공하는 시스템  
**자바스크립트의 모듈 시스템** :

- Common JS (CJS)
- ES Module (ESM)
- AMD
- UMD 등

### Common JS (CJS)

```jsx
// math.js
function add(a, b) {
  return a + b;
}

function sub(a, b) {
  return a - b;
}

module.exports = {
  add,
  sub,
};
```

```jsx
// index.js
const moduleData = require("./math");

console.log(moduleData.add(1, 2)); // 3
console.log(moduleData.sub(1, 2)); // -1
```

```jsx
// index.js 구조 분해 할당
const { add, sub } = require("./math");

console.log(add(1, 2)); // 3
console.log(sub(1, 2)); // -1
```

### ES Module (ESM)

```json
// pakage.json 파일에 모듈 타입을 추가해줘야 함
{
  "type": "module"
}
```

```jsx
// math.js
function add(a, b) {
  return a + b;
}

function sub(a, b) {
  return a - b;
}

export { add, sub };
```

```jsx
// math.js
export function add(a, b) {
  return a + b;
}

export function sub(a, b) {
  return a - b;
}

export default function multiply(a, b) {
  return a * b;
}
```

```jsx
// index.js 구조 분해 할당
import mul, { add, sub } from "./math.js";
// export default로 불러오면 함수명 그대로 사용하지 않아도 됨

console.log(add(1, 2)); // 3
console.log(sub(1, 2)); // -1
```

<br />

## 4. Node.js 라이브러리 사용하기

### 라이브러리란?

프로그램을 개발할 때 필요한 다양한 기능들을 미리 만들어 모듈화 해 놓은 것.  
`npmjs.com` 들어가면 여러 라이브러리를 볼 수 있음!

<br />
<br />
<br />

# React.js

## 1. React.js에 대해 알아보자.

Meta(Facebook)가 개발한 오픈소스 JavaScript 라이브러리.  
대규모 웹 서비스의 UI를 더 편하게 개발하기 위해 만들어진 기술!

### React 특징

- 컴포넌트를 기반으로 UI를 표현한다
- 화면 업데이트 구현이 쉽다(선언형 프로그래밍: 목적만 깔끔하게 명시, 코드가 간결함)
- 화면 업데이트가 빠르게 처리된다(Virtual DOM 이용)

### React App 생성하기

```bash
npm create vite@latest
```

⇒ 만들어진 폴더로 이동 `cd 폴더`

package.json에 있는 `dependencies` 와 `devDependencies` 에 적혀있는 패키지를 설치하기 위해

```bash
npm i
```

⇒ `node_modules` 폴더에 설치가 됨.

<br />

## 2. 컴포넌트

### 1) JSX

JavaScript 확장한 문법으로 JavaScript XML이라는 의미(HTML+JavaScript)

- JSX에서 사용되는 태그의 속성 이름이 다름
- 태그를 명시적으로 닫아줘야 함
- 하나의 태그로 감싸져 있어야 함.

### 2) 컴포넌트

- **React는 컴포넌트라는 단위로 화면이 구성**
- 스스로 상태를 관리하는 캡슐화된 코드 조각
- 하나의 JSX를 반환하는 함수

📍JSX와의 차이점

- 컴포넌트는 기본적으로 함수이기 때문에 자신만의 고유한 로직 존재
- 스스로의 상태 반영 가능

🚨컴포넌트 생성 시 주의 사항

- 컴포넌트 이름은 무조건 PascalCase
- 의미단위로 쪼개어 파일을 분리할 것

### 3) Props

props는 properties의 줄임말로 부모 컴포넌트에서 자식 컴포넌트로 내려주는 데이터이다.

**Props의 활용 팁**

- 구조분해할당 구문 활용
- 특정 Props에 기본 값을 지정 가능
- Pros는 읽기 전용 ‼️

### 4) State

- 컴포넌트 내부에서 사용되는 일종의 **변수**
- 컴포넌트 스스로 상태를 관리하게 만드는 존재

### 5) 클래스형 컴포넌트 vs 함수형 컴포넌트

1. 클래스형 컴포넌트

   - 클래식 문법으로 구현한 옛날 컴포넌트
   - 아직도 사용되고 있고 라이브러리와 서비스에서 활용되고 있기에 학습 필요

1. 함수형 컴포넌트

   - 구조가 클래스보다 단순한 함수를 사용하기로 결정하며 대세로 사용되는 중
   - 코드 재활용성에 유리한 새로운 컴포넌트 제작 방식

1. Hooks 등장 이유
   - 클래스형 컴포넌트의 단점을 보완하기 위해 함수형 컴포넌트에 지급된 개념
