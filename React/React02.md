# Hooks와 이벤트 핸들링

## 1. Hooks 종류

- React의 라이프사이클: 리액트의 동작과정?
- 기본 내장 Hooks는 10가지이며 사용되는 것은 3가지 정도
  - useState
  - useEffect
  - useCallback

### 1) useState

```jsx
const [value, setValue] = useState(인자);
```

useState: 상태 값과 그 값을 갱신하는 함수를 반환

- 인자: 초기 상태 값
- 반환: [상태 변수, 상태에 대한 Setter]

### 2) useEffect

useEffect: 컴포넌트가 렌더링 될 때, 특정 작업을 실행

- 인자
  - 실행하고자 하는 함수 (Effect callback)
    - effect는 정리(clean-up)함수를 반환할 수 있음
    - 반환된 함수는 컴포넌트가 언마운트 또는 effect 재실행 이전에 실행됨
  - 의존성 배열 (dependency list)

<br />

✅ 예시 1: 의존성 배열 없을 때

```jsx
useEffect(() => {
  console.log("[Function] useEffect []: 컴포넌트가 마운트 될 때, 한 번만!"); // (1)

  const eventHandler = () => {
    console.log("click body"); // (2)
  };
  document.body.addEventListener("click", eventHandler);

  return () => {
    console.log("[Function] useEffect return []: 컴포넌트가 언마운트 될 때,"); // (3)
    document.body.removeEventListener("click", eventHandler); // (4)
  };
}, []); // 의존성 배열 비어있음
```

`(1)` : 컴포넌트가 마운트 됐을 때 처음이자 마지막으로 실행됨  
`(2)` : body에 이벤트가 걸려있다. body 클릭 시 실행. 그리고 만약 `(4)` removeEventListener 코드가 없었다면 언마운트 되더라도 계속 실행됨!! body 클릭 시 "click body"가 콘솔에 적힘!!  
`(3)` : ‘클린업 함수’라고 함. 컴포넌트가 언마운트될 때 처음이자 마지막으로 실행됨. `(4)` removeEventListener 코드가 있기 때문에 언마운트 되면 (2)는 더 이상 실행 안됨.

<br />

✅ 예시 2: 의존성 배열 있을 때

```jsx
useEffect(() => {
  console.log(
    "[Function] useEffect [value]: 컴포넌트가 마운트 될 때, + value가 변경되면,"
  ); // (1)

  const eventHandler = () => {
    console.log("click body"); // (2)
  };
  document.body.addEventListener("click", eventHandler);

  return () => {
    console.log(
      "[[Function] useEffect return [value]: 새로 useEffect를 수행하기 전에,"
    ); // (3)
    document.body.removeEventListener("click", eventHandler); // (4)
  };
}, [value]); // 의존성 배열 있음
```

의존성 배열에 변경이 생기면 useEffect 함수가 전체적으로 실행이 됨.(물론 useState 등 다른 코드들도 같이 실행됨). 다시 실행이 될 때 ‘return 문’ 즉 클린업 함수가 실행이 되고 useEffect 함수 내부가 실행이 되는거임.

즉, 콘솔에는

"[Function] useEffect return [value]: 새로 useEffect를 수행하기 전에," (클린업 함수 실행 + 이전 이벤트 리스너 제거)

"[Function] useEffect [value]: 컴포넌트가 마운트 될 때, + value가 변경되면," (useEffect 내부 코드 실행 + 새로운 이벤트 리스너 등록)

으로 콘솔에 출력되는 거임!

클린업 함수가 먼저 실행되는 이유:

- React는 새로운 효과를 적용하기 전에 이전 효과를 정리해야 함
- 이전 useEffect에 의해 설정된 이벤트 리스너나 구독 등을 제거해야 메모리 누수를 방지할 수 있음

<br />

`(1)` : 컴포넌트가 마운트 될 때 한 번 실행됨. 의존성 배열에 변경이 생기면 다시 실행 됨.  
`(2)` : 예시 1과 동일  
`(3)` : 컴포넌트가 언마운트 될 때 한 번 실행됨. 의존성 배열에 변경이 생기면 다시 실행 됨.  
`(4)` : 의존성 배열에 변경될 때 useEffect가 다시 실행되면서 이벤트가 한 번더 생성함. 그래서 `(4)` 코드가 없다면 "click body"가 쌓이게 됨. 그래서 만약 5번 클릭했다? 그럼 5번이 쌓였기 때문에 이제 한 번만 클릭해도 "click body"가 한 번에 5개씩 콘솔에 쌓이게 됨. 그래서 이벤트가 쌓이지 않도록 이벤트를 해제하는 `(4)` 코드를 작성함

### 3) useCallback

useCallback: 메모이제이션된 콜백을 반환

기능보단 성능을 위해 등장함

- 인자
  - 메모이제이션 할 함수
  - 의존성 배열
- 반환: 메모이제이션 된 함수
- 의존성 배열을 제대로 셋팅하지 않으면 함수 내부에서 사용되는 값이 업데이트 되지 않은 값일 수 있음

```jsx
const increaseValue = () => {
  setValue(value + 1);
};
```

이런 함수였다면, 리렌더링 될 때 컴포넌트는 코드 처음부터 렌더링을 시작할거고 increaseValue함수를 만날 때마다 increaseValue함수를 생성할 것임. 결국 기능은 같지만 아예 다른 메모리에 쌓이는 함수를 만들게 됨. 물론 참조하지 않는 함수는 브라우저가 알아서 가비지 컬렉팅(메모리 삭제)을 할 것임. 그래도 성능 등 많은 문제를 일으킴. 그래서 나온 것이 useCallback!

```jsx
const increaseValue = useCallback(() => {
  setValue(value + 1);
}, [value]);
```

useCallback을 사용하면 이전 렌더링에서 생성한 함수를 기억하고 그대로 재활용함 = 메모이제이션  
useCallback도 의존성 배열의 유무에 따라, 의존성 배열이 있다면 값이 바뀔 때마다 새로운 함수를 바꿈  
현 예시는 “value”값만 존재하고, 그 “value”값이 변할 때의 예시임. 그래서 굳이 useCallback을 쓸 필요가 없음.  
그래서 더 좋은 예시를 들자면, 만약 A, B, C 중에 A값만 변하는데 B, C에 관한 함수는 새롭게 생성할 필요가 없음. 그럴 때, B와 C의 함수는 이전 함수를 그대로 사용해도 됨. 이럴 때 useCallback을 사용하여 사이드 이펙트를 차단하는 예시가 있음.  
또는 “reset button”을 만들 때 유용함. reset은 항상 초기화 시키는 버튼이기 때문에 어떠한 값들이 변해도 reset에 관한 함수는 새롭게 생성할 필요가 없으므로 useCallback을 사용하기 좋다.

```jsx
const increaseValue = () => {
  setValue(value + 1);
};
const resetValue = useCallback(() => {
  setValue(0);
}, []);
```

<br />

## 2. Hooks 사용 시 주의사항

[블로그에 작성함](https://velog.io/@yxxnicode/React-Hooks-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD)

<br />

## 3. 렌더링 과정

### 1) React의 렌더링 과정

컴포넌트의 상태, 즉 State 또는 Props가 변경되면서 해당 컴포넌트를 다시 실행하여 화면을 다시 그리는 것을 의미

### 2) React의 라이프 사이클

- **클래스형 컴포넌트의 라이프 사이클**

  - 컴포넌트가 실행되는 것 = 컴포넌트의 마운트
  - 마운트가 되었을 때 constructor 실행 → getDerivedStateFromProps 메서드 실행 → render 메서드 실행 → componentDidMount라는 메서드 실행
  - 메서드에서 컴포넌트를 다시 Rendering할 것인지, 이번 상태 변화에 따른 rerendering은 생략할 것인지를 결정
  - 함수가 false를 반환하면 렌더링을 진행하지 않고, true를 반환하면 렌더링을 진행
  - rendering이 완료되면 마무리로 componentDidUpdate를 실행
  - 컴포넌트가 지워지는 것 = 언마운트

- **함수형 컴포넌트의 라이프 사이클**
  - 마운트 → 바로 자기 자신을 실행 → 반환된 JSX값을 DOM에 반영
    → useEffect라는 Hooke을 실행 → 클래스형 컴포넌트의 componentDidMount, componentDidUpdate, componentWillUnmount 메서드 = useEffect hook으로 통일
    → 업데이트할 때는 다시 함수를 실행 → 그 JSX를 DOM에 반영 → useEffect hook을 실행
  언마운트 시엔 useEffect 함수만 마지막으로 실행

<br />

함수형 컴포넌트 예시

```jsx
import { useEffect, useState } from "react";

function FunctionalComponent() {
  console.log("[Function] Beginning"); // (1)
  const [value, setValue] = useState(0);

  useEffect(() => {
    console.log("[Function] useEffect []"); // (2)

    return () => {
      console.log(
        "[Function] useEffect return []" // (3)
      );
    };
  }, []);

  useEffect(() => {
    console.log("[Function] useEffect [value]"); // (4)

    return () => {
      console.log(
        "[Function] useEffect return [value]" // (5)
      );
    };
  }, [value]);

  console.log("[Function] End"); // (6)

  return (
    <div>
      <h1>FunctionComponent</h1>
      <h1>value: {value}</h1>
      <button
        onClick={() => {
          setValue((state) => state + 1);
        }}
      >
        Increase value
      </button>
    </div>
  );
}

export default FunctionalComponent;
```

함수형 컴포넌트는 JSX를 반환한 뒤 useEffect 호출!

그래서 콘솔에 `(1)` > `(6)` > `(2)` > `(4)` 순으로 출력됨. 그 이후 언마운트 되면 `(3)` > `(5)` 순으로 출력

만약 value값이 변했다면 `(1)` > `(6)` > `(5)` > `(4)` 순으로 출력

<br />

## 4. Form 요소의 이벤트 핸들링

### ControlledComponent (제어 컴포넌트)

React에 의해 입력 요소의 값이 제어되는 컴포넌트

- 장점
  - 컴포넌트의 state와 input value가 완전히 동일한 값을 갖음 (신뢰 가능한 단일 출처)
  - 다른 컴포넌트에 input value를 전달하거나 다른 이벤트 핸들러에서 값을 재설정할 수 있음
- 단점
  - 값이 변경되는 매 순간 렌더링이 됨(해당 컴포넌트의 영향 범위가 클수록 성능 저하)
- 예시

```jsx
import { useState } from "react";

function TextInput() {
  const [value, setValue] = useState("");

  console.log("[TextInput] render", value);

  return (
    <input
      type="text"
      value={value}
      onChange={(e) => {
        setValue(e.target.value);
      }}
    />
  );
}

export default TextInput;
```

<br />

### Uncontrolled Component

React에 의해 입력 요소의 값이 제어되는 컴포넌트

- 예시

```jsx
import { useRef } from "react";

function UncontrolledTextInput() {
  const inputRef = useRef();

  console.log("[UncontrolledTextInput] render");

  return (
    <>
      <input ref={inputRef} type="text" />
      <button
        onClick={() => {
          console.log(inputRef.current.value);
        }}
      >
        Get value
      </button>
    </>
  );
}

export default UncontrolledTextInput;
```

`useRef` 는 특정 DOM 요소의 레퍼런스를 직접 가져올 때 사용함. 자바스크립트에선 document.querySelector를 이용하는데 리액트에선 useRef를 이용하고 `<input ref={inputRef} type="text" />` 으로 속성에 연결시켜주면 요소의 객체 자체를 변수로 가져올 수 있게 되고 요소의 여러가지 정보들을 가져올 수 있음. 현재 예시 코드에서는 button에서 `inputRef.current.value` 로 input의 value값을 가지고 옴.

여기서 inputRef 자체가 DOM요소가 되는 것이 아니라 inputRef.current라는 값에 요소가 담겨 있어서 `inputRef.current.value` 로 접근해서 사용하는 것임.

이 inputRef는 연결된 요소의 값이 바뀌더라도 리렌더링을 발생시키지 않음. 왜냐하면 리렌더링은 state 변화에서만 일어나니까!

<br />

## 5. 실습 설문조사 만들기

- 1번란과 2번란에 값이 없으면 저장 버튼이 활성화가 안됨
- 사는 곳이 한국이라면 2-1번란 생성
- 저장버튼 클릭 시 alert 띄우고 모든 값 초기화

```jsx
// App.js
import { useState } from "react";
import TextInput from "./components/TextInput";
import Select from "./components/Select";

const countryOptions = ["한국", "중국", "일본", "러시아", "미국"];

function App() {
  const [formValue, setFormValue] = useState({
    name: "",
    country: "",
    address: "",
  });

  console.log("[App] formValue", formValue);

  return (
    <div className="App">
      <div className="form">
        <div className="form-item">
          <h1>1. 이름이 무엇인가요?</h1>
          <TextInput
            value={formValue.name}
            setValue={(value) => {
              setFormValue({ ...formValue, name: value.trim() });
            }}
          />
        </div>
        <div className="form-item">
          <h1>2. 사는 곳은 어딘가요?</h1>
          <Select
            value={formValue.country}
            setValue={(value) => {
              setFormValue({ ...formValue, country: value });
            }}
            options={countryOptions}
          />
        </div>
        {formValue.country === "한국" && (
          <div className="form-item">
            <h1>2-1. 한국 어디에 사나요?</h1>
            <TextInput
              value={formValue.address}
              setValue={(value) => {
                setFormValue({ ...formValue, address: value });
              }}
            />
          </div>
        )}
        <div className="button-group">
          <button
            onClick={() => {
              alert("저장되었습니다!");
              setFormValue({
                name: "",
                country: "",
                address: "",
              });
            }}
            disabled={!formValue.name || !formValue.country}
          >
            저장
          </button>
        </div>
      </div>
    </div>
  );
}

export default App;
```

```jsx
// TextInput.js
function TextInput({ value, setValue }) {
  return (
    <input
      type="text"
      value={value}
      onChange={(e) => {
        setValue(e.target.value);
      }}
    />
  );
}

export default TextInput;
```

```jsx
// Select.js
function Select({ value, setValue, options = [] }) {
  return (
    <select value={value} onChange={(e) => setValue(e.target.value)}>
      <option value="" disabled>
        지역을 선택해주세요.
      </option>
      {options.map((item) => (
        <option key={item} value={item}>
          {item}
        </option>
      ))}
    </select>
  );
}

export default Select;
```
