# [실습] SurveyPie, Routing

## SurveyPie와 Admin 소개

### 1. SurveyPie

: 설문조사 서비스

**SurveyPie 기술스택(라이브러리)**

- recoil - 전역 상태 관리
- axios - API 연동
- react-router - 라우팅
- styled-components - 스타일링
- immer-데이터 불변성 유지

### 2. SurveyPie Admin

: SurveyPie에서 서비스될 설문조사 관리(=관리자 사이트)

**SurveyPie 기술스택(라이브러리)**

- redux - 전역 상태 관리
- useSWR, axios - API 연동
- react-router - 라우팅
- styled-components - 스타일링
- immer-데이터 불변성 유지
- antd - UI라이브러리

## SurveyPie 라우터 적용

### 1. Routing이란?

: 주소에 맞게 적절한 페이지를 로드하는 것

브라우저는 페이지 이동에 대한 내용을 기록함!(http 요청 일어남) = 히스토리라는 객체를 가지고 있음.

하지만 리액트는 싱글페이지(SPA)라 http 요청을 보내지 않음.

그럼에도 불구하고 페이지가 이동하는 현상처럼 보이는 이유는?

리액트에서는 **리액트 라우터**라는 라이브러리를 통해 히스토리를 직접 쌓음으로써 페이지가 이동하는 효과를 냄.

### 2. react-router란?

```bash
# 설치
npm install react-router-dom
```

**기존 React Router:**

- 단순히 URL 경로에 따라 어떤 컴포넌트를 보여줄지 결정하는 역할만 했음
- 예: `/about` → About 페이지 컴포넌트 보여주기

**새로운 React Router:**

1. 기본적인 라우팅 (이전과 동일)
   - URL에 맞는 페이지 로드
2. 데이터 처리 (새로운 기능)

   ```tsx
   // 페이지에 필요한 데이터를 자동으로 로드
   export async function loader({ params }) {
     const userData = await fetchUserData(params.id);
     return userData;
   }
   ```

3. 폼 처리 (새로운 기능)

   ```tsx
   // 폼 제출 처리를 라우터 레벨에서 관리
   export async function action({ request }) {
     const formData = await request.formData();
     // 데이터 처리 로직
   }
   ```

4. 자동 데이터 갱신 (새로운 기능)
   - 데이터가 변경되면 관련된 페이지를 자동으로 업데이트

이렇게 단순 페이지 전환을 넘어서 데이터 관리까지 통합된 형태로 제공하는 게 새로운 React Router의 특징!

### 3. react-router 적용

```jsx
// main.jsx
import "./index.css";

import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router"; // 추가

import App from "./App.jsx";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </StrictMode>
);
```

main.jsx 파일에서 App파일을 `BrowserRouter` 로 감싸주기

```jsx
// App.jsx
import "./App.css";

import { Route, Routes } from "react-router-dom"; // 추가

import CompletionPage from "./pages/CompletionPage";
import SurveyPage from "./pages/SurveyPage";

function App() {
  return (
    <div className="App">
      <Routes>
        <Route path="/done" element={<CompletionPage />} />
        <Route path="/survey/:surveyId/:step" element={<SurveyPage />} />
      </Routes>
    </div>
  );
}

export default App;
```

App.jsx파일에서 Routes와 Route 설정!

`path="/survey/:surveyId/:step` : 설문조사 때 누가 한건지 id 받아야겠죠? 그래서 `/:surveyId` 추가!  
만약 1번 질문에 답을 하고, 2번 질문에 답을 하다가 다시 1번 질문으로 넘어가고 싶으서 뒤로가기 또는 이전 버튼을 누르게 되면, 새로운 `/survey/:surveyId` 가 되는 것임. 그래서 새로운 페이지로 넘어가지 않게 `/:step` 을 추가해준 것!

### 3-1. useParams 적용

```jsx
// SurveyPage > index.jsx
import { useState } from "react";
import { useParams } from "react-router-dom";

import ProgressIndecator from "../../components/ProgressIndicator";
import QuestionBox from "../../components/QuestionBox";

function SurveyPage() {
  const params = useParams(); // 추가

  const questions = [
    {
      title: "질문1 입니다.",
      desc: "설명1 입니다.",
      type: "text",
      requird: false,
      options: {},
    },
    {
      title: "질문2 입니다.",
      desc: "설명2 입니다.",
      type: "text",
      requird: true,
      options: {},
    },
  ];

  const step = parseInt(params.step);
  // 문자열로 받아옴. 그래서 parseInt 사용해서 숫자로 변경!

  const [answers, setAnswers] = useState([]);
  return (
    <div>
      <ProgressIndecator />
      <QuestionBox
        question={questions[step]}
        questionsLength={questions.length}
        step={step}
        answers={answers[step]}
        setAnswer={(newAnswer) => {
          setAnswers((answers) => {
            const newAnswers = [...answers];
            newAnswers[step] = newAnswer;

            return newAnswers;
          });
        }}
      />
    </div>
  );
}

export default SurveyPage;
```

### 3-2. useNavigate 적용

Link태그와의 차이!

**Link 태그 :**

- 사용자가 클릭할 수 있는 링크를 만들 때 사용
- HTML의 `<a>` 태그처럼 작동하지만 페이지 새로고침 없이 라우팅
- 주로 네비게이션 메뉴, 목록의 항목 등에 사용

**useNavigate 훅 :**

- 프로그래밍 방식으로 페이지 이동이 필요할 때 사용
- 특정 조건이나 이벤트 후에 페이지 이동이 필요할 때 유용
- 예: 폼 제출 후, 로그인 성공 후, 타이머 완료 후 등

**따라서 :**

- 일반적인 링크는 `Link` 사용
- 조건부 이동이나 프로그래밍적 제어가 필요할 때는 `useNavigate` 사용

```jsx
// ActionButtons > index.jsx
import { useNavigate } from "react-router-dom";

function ActionButtons({ step, qustionsLength }) {
  const isLast = qustionsLength - 1 === step;
  const navigate = useNavigate();

  return (
    <div>
      {step === 0 || (
        <button
          onClick={() => {
            navigate(`/survey/id/${step - 1}`);
          }}
        >
          이전
        </button>
      )}
      {isLast ? (
        <button
          onClick={() => {
            navigate("/done");
          }}
        >
          제출
        </button>
      ) : (
        <button
          onClick={() => {
            navigate(`/survey/id/${step + 1}`);
          }}
        >
          다음
        </button>
      )}
    </div>
  );
}

export default ActionButtons;
```

navigate 안에는 문자열로 넣고 절대경로로 넣어주기! 그냥 넣으면 상대경로여서 경로 맨 뒤에 추가됨.

근데 현재는 id도 값을 받아와야 함.

그래서 App.jsx파일에서

```jsx
<Route path="/survey/:surveyId/:step" element={<SurveyPage />} />
```

이 코드를

```jsx
<Route path="/survey/:surveyId" element={<SurveyPage />}>
  <Route path=":step" element={<SurveyPage />} />
</Route>
```

path 쪼개주기!!

이렇게 하면 앞부분은 유지한채로 step 부분만 상대경로로 인식해서 뒤에만 바뀌게 됨!
