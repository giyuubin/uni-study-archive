# 📖 기말고사 대비 학습 가이드: 웹 프로그래밍
**과정**: 스마트보안 전공 웹 프로그래밍
**범위**: Chapter 9. Next.js

---

## Part 1. Next.js 개요 및 렌더링 전략
React는 오직 UI 컴포넌트 구축에만 집중하는 '라이브러리'이므로 라우팅이나 데이터 패칭, 번들링 기능이 포함되어 있지 않습니다. 반면 **Next.js**는 React 위에서 동작하는 풀스택 **프레임워크**로, 파일 기반 라우팅과 서버 렌더링, 최적화 기능을 기본적으로 제공합니다. 이번 과정에서는 Next.js 16 버전의 **App Router** 방식을 사용합니다.

**웹 페이지 렌더링 전략 (Rendering Strategies)**
* **CSR (Client-Side Rendering)**: 브라우저가 처음에 빈 HTML 껍데기(Shell)만 다운로드하고, 자바스크립트를 실행하여 브라우저 단에서 UI를 그립니다. 첫 로딩은 빠를 수 있으나 검색 엔진 최적화(SEO)에 불리합니다. (추천: 대시보드)
* **SSR (Server-Side Rendering)**: 사용자가 요청할 때마다 서버가 완전한 HTML을 동적으로 생성하여 보냅니다. 첫 화면(First paint)이 빠르고 SEO에 유리합니다. (추천: 동적 페이지)
* **SSG (Static Site Generation)**: 빌드(컴파일) 시점에 미리 HTML을 한 번만 생성해 두고, 이를 CDN을 통해 제공합니다. 가장 빠른 속도와 완벽한 SEO를 제공합니다. (추천: 블로그, 공식 문서)

> *(이상 교안 1~3페이지 내용 통합 요약)*

---

## Part 2. App Router 기반 프로젝트 구조
Next.js의 라우팅과 주요 파일들은 정해진 폴더 구조 규칙을 따릅니다.

* **`app/` 디렉토리**: 모든 URL 라우트의 최상위 폴더입니다.
* **주요 특수 파일**
    * `layout.tsx`: 하위 페이지들을 감싸는 공통 UI입니다. 하위 라우트로 이동하더라도 다시 렌더링되지 않습니다. **최상위(Root) 레이아웃은 반드시 `<html>`과 `<body>` 태그를 포함해야 합니다.**
    * `page.tsx`: 특정 라우트의 실제 고유 콘텐츠를 정의합니다. 특정 폴더에 `page.tsx`가 없다면 404 에러가 반환됩니다.
    * `loading.tsx`: 페이지 데이터가 로딩되는 동안 표시될 UI입니다.
    * `error.tsx`: 처리되지 않은 에러가 발생했을 때 렌더링됩니다.
    * `not-found.tsx`: `notFound()` 함수가 호출되거나 존재하지 않는 경로일 때 렌더링됩니다.
* **기타 주요 폴더**: `components/ui/`(UI 컴포넌트), `contexts/`(전역 상태), `lib/`(공통 유틸 함수 및 API), `types/`(타입스크립트 정의)
* **경로 별칭 (Path Alias `\@/`)**: `../../lib/api`와 같이 복잡하고 오류가 나기 쉬운 상대 경로 대신, 프로젝트 루트를 가리키는 `\@/`를 사용하여 `import { api } from '\@/lib/api';`처럼 어디서든 깔끔하게 경로를 참조할 수 있습니다.

> *(이상 교안 4~6페이지 내용 통합 요약)*

---

## Part 3. Layout과 Page의 역할, 그리고 Metadata
* **Root Layout (`app/layout.tsx`)**
    * 프로젝트의 모든 라우트가 공유하는 가장 큰 틀입니다.
    * 여기서 `<AuthProvider>{children}</AuthProvider>`와 같이 자식 컴포넌트들을 Context Provider로 감싸주면, 프로젝트 내의 모든 페이지에서 인증(Auth) 상태에 접근할 수 있게 됩니다.
* **Route Content (`app/page.tsx`)**
    * 최상위 `page.tsx`는 보통 **인증 가드(Auth Guard) 및 리다이렉션 역할**을 수행합니다.
    * 로딩(`isLoading`) 상태가 끝날 때까지 기다렸다가, 사용자(`user`) 정보가 있으면 `/dashboard`로, 없으면 `/auth/login`으로 보내는 로직을 `useEffect` 내부에 작성합니다.
* **Metadata API**
    * `<head>` 태그 내의 메타데이터(제목, 설명 등)를 채우기 위해 `export const metadata: Metadata = { title: '...', description: '...' }` 형태의 객체를 내보냅니다.
    * **주의**: 메타데이터 기능은 오직 **Server Component에서만 동작**하므로, `'use client'` 지시어와 함께 사용할 수 없습니다.

> *(이상 교안 7~9페이지 내용 통합 요약)*

---

## Part 4. Server Component vs Client Component
Next.js 13 이상(App Router)에서는 성능 최적화를 위해 기본적으로 모든 컴포넌트가 서버에서 렌더링됩니다. 상호작용이 필요한 곳에만 선택적으로 클라이언트 컴포넌트를 적용해야 합니다.

* **Server Component (기본값)**
    * 서버에서 미리 렌더링되어 HTML로 전달됩니다.
    * **제한사항**: `useState`, `useEffect` 같은 React Hook을 사용할 수 없으며, `onClick` 같은 이벤트 핸들러도 붙일 수 없습니다.
    * 정적이고 사용자와의 상호작용이 없는 UI(예: 단순 텍스트, 레이아웃 골격)에 적합합니다.
* **Client Component**
    * 브라우저에서 렌더링되며, 사용자와의 상호작용이 필요한 경우 사용합니다.
    * **사용 방법**: 파일의 **가장 첫 번째 줄**에 반드시 `'use client';` 지시어를 작성해야 합니다.
    * 이 선언 이후에는 상태 관리(Hook), 이벤트 처리, 브라우저 API(`localStorage` 등)를 자유롭게 사용할 수 있습니다.

> *(이상 교안 10~11페이지 내용 통합 요약)*

---

## Part 5. 라우팅 및 네비게이션 (Routing & Navigation)
Next.js는 라우터 설정 파일 없이 폴더 구조만으로 라우팅을 자동으로 처리합니다(File-based Routing).

* **정적 라우팅**: `app/dashboard/page.tsx` 파일은 자동으로 `/dashboard` 주소와 매핑됩니다.
* **동적 라우팅 (Dynamic Routes)**
    * 폴더명을 대괄호로 묶으면(`[id]`) 해당 위치에 들어오는 어떤 URL 값이든 변수로 캡처합니다.
    * 예: `app/reports/[id]/page.tsx`는 `/reports/1` 등의 요청을 처리하며, 이 '1'이라는 값은 컴포넌트의 **`params` Prop**을 통해 `params.id` (문자열) 형태로 전달받아 API 호출 등에 사용할 수 있습니다.
* **네비게이션 방식**
    * **`<Link>` 컴포넌트**: 정적이거나 동적인 경로로 이동할 때 사용합니다. 브라우저의 전체 새로고침 없이 부드럽게 화면을 전환하며, 백그라운드에서 연결된 페이지를 자동으로 미리 가져오는(Pre-fetch) 강력한 최적화 기능을 갖추고 있습니다.
    * **`useRouter` Hook**: 버튼 클릭, 로그인 성공 등 **로직 처리에 따른 프로그램적인 이동**이 필요할 때 사용합니다. `router.push('/경로')`를 호출하면 해당 경로로 이동하며 브라우저 히스토리가 추가됩니다.

> *(이상 교안 12~15페이지 내용 통합 요약)*

---

## Part 6. API 연동 및 환경 변수 관리
프론트엔드에서 백엔드 서버와 안전하고 체계적으로 통신하는 방법입니다.

* **환경 변수**: 브라우저(클라이언트) 코드에서 접근해야 하는 환경 변수는 반드시 `NEXT_PUBLIC_` 접두사를 붙여야 합니다. (`NEXT_PUBLIC_API_URL=http://localhost:3000`). 이 값은 `.env.local`에 저장하며 깃(Git)에는 올리지 않습니다.
* **중앙 집중형 API 클라이언트 (`lib/api.ts`)**
    * HTTP 요청 로직을 한 곳에 모아 관리합니다.
    * `parseOrThrow` 헬퍼 함수를 만들어, `response.ok`가 아닐 경우(상태 코드가 2xx가 아닐 때) 강제로 에러를 던지도록 처리합니다. 이를 통해 컴포넌트 단에서는 깔끔하게 성공/실패만 다룰 수 있습니다.
    * **GET vs POST**: GET은 조회용이므로 `Authorization` 헤더(토큰)만 넘기지만, POST는 데이터를 생성하므로 `Content-Type: application/json` 헤더와 `body: JSON.stringify(data)`가 추가로 필요합니다.
* **JWT 토큰 관리 및 인증 흐름**
    * **로그인 시**: API 응답으로 받은 토큰과 유저 정보를 브라우저의 `localStorage.setItem`을 이용해 저장하고, React State(`setToken`, `setUser`)도 업데이트합니다.
    * **페이지 새로고침 시**: 초기화(Mount) 단계인 `useEffect`에서 `localStorage.getItem('token')`으로 토큰을 복구하여 로그인 상태를 유지시킵니다.
    * **보호된 페이지에서의 API 호출**: 페이지 컴포넌트 안에서 `useEffect`를 사용해 데이터를 패칭합니다. 이때 토큰이 없다면 `router.push`로 강제 로그인 페이지로 쫓아내고, 데이터가 오기 전까지는 `<div>Loading...</div>` 같은 로딩 상태를 반환(Early Return)하여 에러를 막습니다.

> *(이상 교안 16~20페이지 내용 통합 요약)*

---

## Part 7. UI 컴포넌트 라이브러리 (shadcn/ui)
빠르고 일관된 디자인 시스템 구축을 위해 **shadcn/ui**를 사용합니다.

* **특징**: npm 모듈 형태의 외부 종속성이 아니라, 설치 시 소스 코드가 프로젝트의 `components/ui/` 폴더 안으로 직접 복사됩니다. 즉, 개발자가 코드를 완벽히 소유하고 입맛에 맞게 커스텀(Tailwind CSS 활용)할 수 있습니다. Radix UI 프리미티브를 기반으로 제작되어 접근성이 뛰어납니다.
* **핵심 컴포넌트 활용법**
    * **Card**: 정보 묶음을 표시할 때 사용합니다. 내부는 `<CardHeader>`, `<CardTitle>`, `<CardContent>` 등으로 계층화되어 있습니다.
    * **Badge**: 카테고리나 상태를 나타내는 태그입니다. `variant` 속성을 통해 색상/스타일을 제어합니다. (예: 기본값, `variant="secondary"`, `variant="destructive"`)
    * **Select**: 드롭다운 선택 메뉴입니다. 선택 값이 변경될 때 발생하는 이벤트는 `onValueChange={(v) => setCategory(v)}`의 형태로 받아 상태를 업데이트합니다.
    * **Toast (sonner)**: 작업의 성공/실패를 알리는 임시 알림창입니다. 이벤트 수행 후 `toast.success('메시지')` 또는 `toast.error('메시지')`를 호출하여 사용자에게 피드백을 줍니다.

> *(이상 교안 21~23페이지 내용 통합 요약)*
