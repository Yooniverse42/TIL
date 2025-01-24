# Recoil, Axios

## 1. Recoil

`Atom` 이라는 저장소를 가짐. 근데 하나가 아닌 여러 개의 저장소를 가짐. **useState 상태와 비슷함**.

하지만 useState는 하나의 컴포넌트 안에서만 사용되고, `Atom` 은 여러개의 컴포넌트에서 사용 가능.

`Selector`는 `Atom`에 있는 데이터 중 원하는 데이터를 `Selector`에서 가공해서 사용함

만약 `Atom`에 여러 과일이 담겨있음. A라는 컴포넌트에서는 그 과일을 모두 사용함. 근데 B라는 컴포넌트에서는 빨간색인 과일만 사용하고 싶음. 이때, B컴포넌트에서 `Selector`를 이용해서 빨간색만 필터링해서 쓰는거임!

그래서 `Atom`에 다른 과일이 추가가 되더라도, A컴포넌트에서는 모든 과일을 사용하기 때문에 **리렌더링이 일어나**지만, B라는 컴포넌트에서는 빨간색이 아니라면 사용하지 않아서 불필요한 리렌더링이 방지됨!

- `Atom`
  - 상태(state)의 기본 단위
  - 사용 가능한 훅:
    - `useRecoilState()`: [value, setValue] 형태로 상태와 setter 함수 둘 다 반환
    - `useRecoilValue()`: 상태값만 반환, 값만 읽기 가능
    - `useSetRecoilState()`: setter 함수만 반환

```jsx
const textState = atom({
  key: "textState", // unique ID
  default: "", // default valuse
});
```

```jsx
const [text, setText] = useRecoilState(textState);
```

- `Selector`
  - `get`만 정의된 경우: 읽기 전용
  - `get`과 `set`이 모두 정의된 경우: 읽기/쓰기 모두 가능
  - 사용 가능한 훅:
    - `useRecoilState()`: selector에 set이 정의되어 있을 때 사용 가능
    - `useRecoilValue()`: 상태값만 반환, 값만 읽기 가능
    - `useSetRecoilState()`: selector에 set이 정의되어 있을 때 사용 가능

```jsx
const charCountState = selector({
  key: "charCountState", // unique ID
  get: ({ get }) => {
    const text = get(textState);

    return text.length;
  },
});
```

```jsx
function CharacterCount() {
  const count = useRecoilValue(charCountState); // 단일 값으로 setter를 반환하지 않음

  return <>Character Count : {count}</>;
}
```

### 1-1. 전역 상태 사용에 있어 두 가지 고민

1. 어떤 데이터를 전역 상태로 관리할 것인가? ⇒ 여러 컴포넌트에서 공유되어 사용되는 데이터
2. 스토어와 어떤 컴포넌트를 연결할 것인가? ⇒ 가장 렌더링 영향이 적은 컴포넌트(하위 컴포넌트에 연결, 만약 하나의 관심사로 grouping이 되어 있다면 상위 컴포넌트에 전역으로 연결)

## Custom Hook

: 하나 이상의 Hooks를 조합해서 만든 새로운 Hook

## Axios

: API를 쉽게 연동할 수 있는 라이브러리

포켓베이스같은 서버를 사용할 땐, 최적의 메소드가 있어서 필요성을 못느끼겠지만, GitHub나 날씨 API 등을 사용할 때 자바스크립트 내장 함수인 fetch 메서드보다 Axios가 사용하기 편리해서 쓰는거임!
