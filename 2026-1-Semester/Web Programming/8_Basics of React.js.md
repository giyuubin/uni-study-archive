# 📖 기말고사 대비 학습 가이드: 웹 프로그래밍
**과정**: 스마트보안 전공 웹 프로그래밍
**범위**: Chapter 8. Basics of React.js

---

## Part 1. HTML과 Vanilla JS의 기초 및 한계
React를 이해하기 위해서는 그 근간이 되는 HTML 문서 구조와 순수 자바스크립트(Vanilla JS)의 한계를 먼저 파악해야 합니다.

* **HTML 기본 구조**
    * HTML은 프로그래밍 언어가 아닌 웹 페이지의 시각적 구조를 정의하는 마크업 언어입니다. 브라우저는 HTML 태그(`<tag>`)를 파싱하여 화면을 그립니다.
    * `<!DOCTYPE html>`로 HTML5 문서임을 선언하며, 크게 메타데이터를 담는 `<head>`와 실제 브라우저에 렌더링되는 콘텐츠를 담는 `<body>`로 구성됩니다.
    * **주요 태그와 시맨틱 마크업**: 계층을 나타내는 `h1~h6`, 이미지를 삽입하는 `img`(접근성을 위해 `alt` 속성 필수), 하이퍼링크 `a`, 목록 `ul`/`li` 등이 있습니다. 특히 `header`, `nav`, `main`, `footer` 같은 시맨틱 태그는 의미를 부여하며, 단순 구역을 나눌 때는 블록 레벨의 `div`, 인라인 레벨의 `span`을 사용합니다.
    * **폼(Form)과 속성**: 데이터를 입력받기 위해 `<form>`, `<input>`, `<label>`, `<button>`을 사용합니다. `<label>`의 `for` 속성은 `<input>`의 `id`와 연결됩니다. 각 요소는 고유 식별자 `id`, CSS 스타일을 위한 `class`, 입력 제어를 위한 `type`, `placeholder` 등의 속성(Attribute)을 가집니다.
* **DOM (Document Object Model)과 조작**
    * 브라우저는 HTML 문서를 메모리 상에 트리(Tree) 구조로 모델링하는데 이를 DOM이라고 부릅니다. 트리의 최상위 루트는 `document`입니다.
    * `<script src="app.js" defer></script>`를 통해 외부 자바스크립트를 불러오며(`defer`를 통해 HTML 파싱 후 실행 보장), JS는 이 DOM 트리를 읽고 수정합니다.
    * `getElementById`나 `querySelector`로 요소를 선택하고, `addEventListener`를 통해 이벤트를 처리합니다. 폼 제출 시 페이지가 새로고침되는 기본 동작을 막기 위해 `event.preventDefault()`를 필수적으로 사용합니다.
* **Vanilla JS의 한계**
    * 규모가 커질수록 순수 JS로 DOM을 일일이 수동 업데이트하는 것은 코드가 길어지고 오류가 발생하기 쉽습니다. UI 상태가 여러 곳에 분산되고, 로직과 마크업이 강하게 결합되어 재사용성이 떨어집니다. 이는 곧 React 컴포넌트 모델의 등장 배경이 됩니다.

> *(이상 교안 1~12페이지 내용 통합 요약)*

---

## Part 2. React의 등장과 가상 DOM (Virtual DOM)
Vanilla JS의 복잡성을 해결하기 위해 Meta(Facebook)에서 개발한 UI 구축용 자바스크립트 라이브러리입니다.

* **핵심 철학**: 어플리케이션의 View 계층에 집중하며, '어떻게(How)' 그릴지가 아니라 '무엇을(What)' 그릴지 선언(Declarative)합니다. UI는 재사용 가능하고 독립적인 상태를 가지는 **컴포넌트(Component)들의 트리 구조**로 이루어집니다.
* **Virtual DOM (가상 DOM)과 재조정(Reconciliation)**
    * React는 실제 DOM 대신 메모리 상에 가상 DOM을 유지합니다.
    * 상태(State)가 변경되면 새로운 가상 DOM 트리를 생성하고, 이전 트리와 비교(Diffing)합니다.
    * 비교 결과 변경된 노드만 실제 DOM에 효율적으로 업데이트(Re-render)하여 무거운 쓰기 작업을 최소화합니다.

> *(이상 교안 13~14페이지 내용 통합 요약)*

---

## Part 3. JSX 문법 규칙
JSX는 자바스크립트 안에서 HTML과 유사한 마크업을 작성할 수 있게 해주는 문법 확장입니다. 필수는 아니지만 컴포넌트 구조를 직관적으로 만들어주어 표준으로 사용됩니다. (TypeScript 환경에서는 `.tsx` 확장자 사용)

* **JSX 필수 작성 규칙**
    1.  HTML의 `class` 대신 **`className`**을 사용합니다.
    2.  이벤트 핸들러 속성은 반드시 **카멜케이스(camelCase)**로 작성합니다. (예: `onClick`, `onChange`)
    3.  컴포넌트는 반드시 단일 루트 요소를 반환해야 하므로, 형제 요소들은 **Fragment (`<> ... </>`)** 로 감싸야 합니다.
* **표현식 삽입**
    * JSX 내에서 자바스크립트 변수나 표현식을 사용할 때는 중괄호 `{}`로 감쌉니다. (예: `<span>{user?.username}</span>`)
    * JSX 내부에서는 `if`나 `for` 같은 문장(Statement)은 사용할 수 없으며, 조건부 출력이 필요할 때는 **삼항 연산자(`? :`)**를 사용합니다.

> *(이상 교안 15~17페이지 내용 통합 요약)*

---

## Part 4. 컴포넌트 (Components)와 Props
* **컴포넌트의 정의**: JSX를 반환하는 자바스크립트 함수입니다. 이름은 반드시 **대문자로 시작**해야 하며, 단일 파일 당 하나의 컴포넌트를 작성하는 것이 관례입니다. 페이지 컴포넌트는 `default export`를 사용합니다.
* **Props (단방향 데이터 흐름)**
    * 부모 컴포넌트가 자식 컴포넌트에게 전달하는 **읽기 전용(Read-only)** 입력 데이터입니다. 데이터는 오직 위에서 아래로만 흐릅니다.
    * 예: `<UserBadge role={user.role} />` 처럼 전달하고, 자식은 `function UserBadge({ role })` 형태로 받습니다.
* **`children` Prop**
    * 부모가 자식 컴포넌트의 태그 사이에 넣은 중첩된 JSX 콘텐츠를 의미합니다. 타입은 `React.ReactNode`를 사용하며, 주로 레이아웃이나 Provider 컴포넌트를 만들 때 활용됩니다.

> *(이상 교안 18~20페이지 내용 통합 요약)*

---

## Part 5. React Hooks: 상태와 생명주기 관리
클래스 없이 함수형 컴포넌트에서 React의 핵심 기능을 사용할 수 있게 해주는 특별한 함수들입니다. 반드시 컴포넌트의 최상단에서 호출해야 하며, 반복문이나 조건문 내부에서는 사용할 수 없습니다.

* **상태 관리: `useState`**
    * 컴포넌트의 로컬 상태를 관리합니다. `const [email, setEmail] = useState('');` 형태로 초기값을 넘겨주며, 현재 상태값과 이를 변경하는 상태 변경 함수(Setter)를 배열로 반환합니다.
    * 상태 업데이트는 비동기적으로 일괄 처리(Batched)됩니다. 상태값을 직접 수정(Mutate)하면 안 되며, **반드시 Setter 함수를 사용**해야 컴포넌트가 리렌더링됩니다. (제어 컴포넌트 패턴: `value={email} onChange={(e) => setEmail(e.target.value)}`)
* **사이드 이펙트 관리: `useEffect`**
    * API 데이터 패칭, 타이머 설정, DOM 수동 조작 등 렌더링 외적인 작업(Side effect)을 처리할 때 사용되며, 컴포넌트 렌더링 이후에 실행됩니다.
    * **의존성 배열(Dependency Array)**:
        * `[]`: 컴포넌트가 처음 마운트될 때 딱 한 번만 실행됩니다.
        * `[token]`: 마운트 시 및 `token` 값이 변경될 때마다 실행됩니다.
        * (배열 없음): 매 렌더링 직후마다 실행됩니다.
    * **클린업 함수(Cleanup Function)**: `useEffect` 내부에서 함수를 `return` 하면, 컴포넌트가 언마운트되기 전이나 이펙트가 재실행되기 직전에 자원(타이머 등)을 정리하여 메모리 누수를 방지합니다.

> *(이상 교안 21~26페이지 내용 통합 요약)*

---

## Part 6. 이벤트 처리와 동적 렌더링
* **이벤트 핸들링**: React는 브라우저 기본 이벤트를 감싼 '합성 이벤트(Synthetic events)'를 사용합니다. 폼 제출 시에는 `e.preventDefault()`를 호출해 기본 동작을 차단하고 별도의 API 호출 로직 등을 연결합니다.
* **조건부 렌더링 (State-based Rendering)**
    * 데이터가 아직 로드되지 않았거나 에러가 났을 때는 `if (!stats) return <div>Loading...</div>;` 처럼 **Early return**을 사용합니다.
    * 조건이 참일 때만 렌더링하려면 `&&` 연산자를, 두 요소 중 하나를 선택할 때는 삼항 연산자를 사용합니다.
* **배열 렌더링 (`.map()`)**
    * 배열 데이터를 JSX 요소 리스트로 변환할 때 `.map()` 함수를 사용합니다.
    * 배열 내의 각 항목은 요소의 변경/추가/삭제를 React가 효율적으로 추적할 수 있도록 고유하고 안정적인 식별자를 **`key` Prop**으로 반드시 가져야 합니다. (배열의 인덱스 사용은 지양)

> *(이상 교안 27~29페이지 내용 통합 요약)*

---

## Part 7. 상태 공유의 구원자: Context API
컴포넌트 트리가 깊어질수록, 중간 계층의 컴포넌트들이 실제로는 쓰지 않는 Props를 계속해서 자식에게 전달만 해야 하는 **프롭 드릴링(Prop Drilling)** 문제가 발생합니다. 이를 해결하기 위해 전역적으로 값을 공유하는 Context API를 사용합니다.

1.  **Context 생성 (`createContext`)**: 공유할 데이터의 타입과 초기값(보통 `undefined`)을 지정하여 Context 객체를 생성합니다.
2.  **Context 제공 (`Provider`)**: `AuthContext.Provider`처럼 Provider 컴포넌트를 만들어 상태를 관리하고 하위 트리를 감싸줍니다(`{children}`). `value` prop으로 상태와 함수(`user`, `login` 등)를 내려주면, Provider 내부 상태가 바뀔 때마다 이를 구독하는 컴포넌트들이 렌더링됩니다.
3.  **커스텀 Hook 활용 (`useAuth`)**: `useContext(AuthContext)`를 래핑하여 에러 가드를 추가한 커스텀 Hook을 만듭니다. 이를 통해 Provider 외부에서 접근하는 실수를 방지하고 간편하게 상태를 꺼내 쓸 수 있습니다.
4.  **루트 등록**: `layout.tsx`와 같은 앱의 최상단에서 `<AuthProvider>`로 전체 앱을 감싸 모든 페이지에서 Context에 접근할 수 있게 합니다.

> *(이상 교안 30~34페이지 내용 통합 요약)*

---

## Part 8. [Lab] Next.js 초기 세팅 실습 요약
* **목적 및 핵심 개념**: `npx create-next-app@latest` 명령어를 통해 최신 Next.js 프로젝트 기반을 생성합니다. 
* 이 과정을 통해 React와 Next.js 구동에 필요한 라이브러리(`react`, `react-dom`, `next`) 및 Tailwind CSS 등의 필수 의존성이 자동 설치되며, App Router 기반의 권장 구조로 개발 환경이 즉시 셋업됩니다.

> *(이상 교안 35페이지 내용 요약)*
