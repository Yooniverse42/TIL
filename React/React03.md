# [실습] 메모장 만들기

## input값 변경안됨 이슈 이유 및 해결

```jsx
// App.jsx
import { useState } from "react";
import "./App.css";
import MemoContainer from "./components/MemoContainer";
import SideBar from "./components/SideBar";

function App() {
  const [memos, setMemos] = useState([
    {
      title: "Memo1",
      content: "This is memo 1",
      createdAt: 1736767667012,
      updatedAt: 1736767667012,
    },
    {
      title: "Memo2",
      content: "This is memo 2",
      createdAt: 1736767701507,
      updatedAt: 1736767701507,
    },
  ]); // 임시 데이터

  const [selectedMemoIndex, setSelectedMemoIndex] = useState(0);
  // 0 : 임시데이터 배열 중 0번째

  // setMemo의 문제점!!!
  const setMemo = (newMemo) => {
    memos[selectedMemoIndex] = newMemo;

    console.log("memos", memos);

    setMemos(memos);
  };

  return (
    <div className="App">
      <SideBar memos={memos} />
      <MemoContainer memo={memos[selectedMemoIndex]} setMemo={setMemo} />
    </div>
  );
}

export default App;
```

```jsx
// MemoContainer.jsx
function MemoContainer({ memo, setMemo }) {
  return (
    <div className="MemoContainer">
      <input
        type="text"
        className="MemoContainer__title"
        value={memo.title}
        onChange={(e) => {
          setMemo({
            ...setMemo,
            title: e.target.value,
            updatedAt: new Date().getTime(),
          });
        }}
      />
      <textarea
        className="MemoContainer__content"
        value={memo.content}
        onChange={(e) => {
          setMemo({
            ...setMemo,
            content: e.target.value,
            updatedAt: new Date().getTime(),
          });
        }}
      />
    </div>
  );
}

export default MemoContainer;
```

여기서 input에 값을 변경하려고 하면 변경이 안됨

하지만 `setMemo함수`에 conosle.log를 넣고 확인해보면, input값은 변경이 안되지만 콘솔에 출력되는 것을 확인할 수 있음.

근데 왜 `setMemos(memos);`가 변경이 안되냐?

- 이유1. setter 함수의 작동원리
  현재 selectedMemoIndex가 0으로 셋팅되어 있음. 하지만 내가 수정할 값이 또 0이라면 setter함수는 이전 값과 똑같은 값이므로 상태를 변경할 필요가 없다고 생각하여 리렌더링이 일어나지 않음. 즉, `setMemos(memos);`에서 값이 똑같다고 인식을 한거임. 근데 분명 input값 변경됐다? === newMemo에 값이 들어왔다 인데 왜 상태가 똑같다고 생각해서 값이 변경이 안되냐? 아래 이유2 확인.
- 이유2. 자바스크립트의 참조(Reference) 개념
  자스에는 기본형(===원시형)과 참조형 변수 타입으로 나뉨. 객체, 배열, 함수가 대표적인 참조형임. `Reference` 는 일종의 객체의 주소! 현재 memos에 배열로 선언했죠? 이렇게 선언하면 memos가 그 배열 그대로 가지고 있는 것이 아니라 메모리에 할당이 됨. 그 메모리 안에 값이 들어있겟죠?. 즉, memos는 선언한 배열이 아닌 그 배열을 가리키는 레퍼런스를 들고 있는 것임. 그래서 내가 input값 변경 시도 === memos의 첫 번재 index 변경 시도했을 때, memos의 레퍼런스를 변경한 것이 아닌 찐 배열이 변경된 것임. 그 후 실제 내용 자체로는 바뀌었지만 동일한 레퍼런스를 setMemos에 넣어주다 보니 당연히 이전 memos값과 동일하다고 판단해버림!

그럼 memos가 다른 레퍼런스를 갖기위해선 어떤 식으로 구현해야 하냐? `setMemos(memos);` 가 아닌 `setMemos(...memos);` 으로, 객체 하나하나를 배열에서 꺼낸 다음 다시 배열로 감싸주게 하면 됨. 그럼 이 새로운 배열은 다른 메모리에 할당을 하게 됨.

그리고 `setMemo` 함수에는 문제점이 하나 더 있음. 렌더링이 되기 직전까지는 memos의 데이터가 훼손이 되면 안됨. 즉 불변성이 있어야함. 하지만 `memos[selectedMemoIndex] = newMemo;` 로 memos에다가 직접적으로 newMemo를 넣어준거임. 즉, 현재 state에 값을 변경해버리는 꼴이 되버림(===불변성 훼손). 물론 현재 코드는 간단해서 큰 문제는 안되지만, 만약 렌더링을 하기 직전에 다른 곳에서 memos에 접근을 하면 훼손된 memos에 접근을 하게 된다는거임. 그럼 시스템 전체적으로 불확실한 동작을 하게 될 것이고 라이프 사이클이 훼손될 것임.

그럼 방법은 memos를 훼손하지 않고 미리 memos를 copy하여 사용하면 됨.

```jsx
// memos 훼손
const setMemo = (newMemo) => {
  memos[selectedMemoIndex] = newMemo; // 훼손됨!

  setMemos(...memos);
};

// memos copy
const setMemo = (newMemo) => {
  const newMemos = [...memos]; // 먼저 memos를 복사해놓음!
  newMemos[selectedMemoIndex] = newMemo; // 그 후 새로운 값 집어넣기!

  setMemos(...memos);
};
```

<br />
<br />

## useCallback 이해 부족 이슈..

```jsx
const setMemo = useCallback(
  (newMemo) => {
    const newMemos = [...memos];
    newMemos[selectedMemoIndex] = newMemo;
    setMemos(newMemos);
    localStorage.setItem("memo", JSON.stringify(newMemos));
  },
  [memos, selectedMemoIndex] // 의존성 배열에 memos 존재
);
```

```
const setMemo = useCallback(
  (newMemo) => {
    setMemos((memos) => { // 함수형 업데이트 사용
      const newMemos = [...memos];
      newMemos[selectedMemoIndex] = newMemo;
      localStorage.setItem("memo", JSON.stringify(newMemos));

      return newMemos;
    });
  },
  [selectedMemoIndex] // 의존성 배열에서 memos 삭제 가능
);
```

### 1. 함수형 업데이트 사용 이유

1번 코드에서 굳이 2번코드로 바뀐 이유에 대해서 이해를 못했다..  
어느 컴포넌트에서든 memos가 연결되어 있고 memos가 변경될 때마다 어차피 setMemo함수가 실행이 되어야 하는데 왜 함수형 업데이트를 사용해야 하는지?

ㅋㅋ 이건 나의 리액트와 useCallback에 대한 이해 부족이 문제였다.

결론은 memos가 변경되는 것과 setMemo가 새로운 함수를 만드는건 상관이 없는 일이다.  
즉, 의존성 배열에 memos가 있으면 memos가 변경될 때마다 새로운 setMemo함수가 만들어진다(다른 참조)는 말이다. 값이 변경되는 것과 함수가 새로 만들어져서 참조가 달라진다는 것은 상관이 없다. 그래서 함수 자체가 새롭게 리렌더링(새로운 함수)할 필요가 없으므로 함수형 업데이트를 사용하며 의존성 배열에서 memos를 삭제시킨 것이다.

위 코드에서 보면 `selectedMemoIndex` 도 상관없는거냐 할 수 있다. 맞다 상관없어서 함수형 업데이트를 사용해도 된다. 하지만 그렇게 해서 얻는 이점보다 코드가 복잡해져서 안한거다..ㅋ

<br />

### 2. 함수형 업데이트 사용 시 return문 필요한 이유

새로운 상태가 무엇이 될지를 React에게 알려주는 역할을 한다.

return을 사용함으로써 새로운 상태를 반환할 것이고 이 반환값이 다음 렌더링에서 사용될 새로운 `memos` 상태가 된다. 만약 return이 없다면, undefined가 반환되어 `memos`가 undefined가 될 것이다.
