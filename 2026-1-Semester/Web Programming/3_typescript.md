- TypeScript
  1. TypeScript의 도입 배경과 거시적 구조(JS vs TS)
    - TS는 JS의 모든 문법을 포함하면서 그 위에 정적 타입(Static Type)이라는 방어박을 씌운 언어다
    - <img width="469" height="280" alt="image" src="https://github.com/user-attachments/assets/fc1e33b1-94c3-41bd-8e96-d84ae38ff475" />
      1. JS: 기존 구형 JS 문법
      2. ES6: 현대 JS 문법(let/const, 화살표 함수 등)
      3. TS: JS, ES6를 포함하며, interfaces, strongly typed, generics가 덧붙여있다
      4. 화살표 흐름: 웹 브라우저나 Node.js는 타입스크립트를 직접 읽지 못하므로, 실행 전 JS로 변환(컴파일)해야 한다
    - JS vs TS
    - <img width="869" height="268" alt="image" src="https://github.com/user-attachments/assets/a5381e72-2846-42a7-a0ed-8081ba48fc7e" />
      - 에러 발견 시점: JS는 코드가 실제 돌아갈 때 프로그램이 뻗어버리지만, TS는 코드를 짜고 변환하는 Compile time에서 미리 에러를 잡아준다
      - 실행 방식: JS는 파일(.JS) 그대로 실행되지만, TS(.ts)는 반드시 변환 과정을 거쳐야한다
  2. TS 컴파일 환경 아키텍처
    - TypeScript Compiler(tsc): TS 코드를 JS로 번역해줌
      - 동작원리: tsc는 코드의 타입 규칙이 맞는지 검사한 뒤, 통과하면 타입 정보를 지우고 순수한 JS 파일로 번역한다
      - 환경 설정(tsconfig.json):
        - package.json이 프로젝트의 신분증이었다면, tsconfig.json은 번역기의 작업 지시서이다
        - 터미널에 tsc --init을 입력하면 생성되며, 번역할 결과물의 JS 버전, 모듈 시스템 종류, 번역된 파일이 저장된 폴더, 타입 검사를 얼마나 엄격하게 할지를 거시적으로 제어한다
      - ts-node: 개발 단계에서 매번 번역(tsc)하고 실행(node)하는 두 단계를 한 번에 합쳐서 실행해주는 개발용 테스트 도구
  3. 기본 타입 시스템
    - TS에서는 변수나 함수 매개변수 이름 뒤에 :를 붙여 데이터의 형태를 강제한다
    - <img width="396" height="243" alt="image" src="https://github.com/user-attachments/assets/bd6bc8ea-0635-481a-967c-d75ce08ca55b" />
    - 기본 타입: string, number(정수/소수 통합), boolean
    - 특수 타입
      - any: 타입 검사를 무력화킨다 // JS처럼 절대 사용하면 안 됨
      - void: 함수가 아무런 결과도 return하지 않고 정상적으로 끝날 때 사용
      - never: 함수가 의도적으로 시스템 종료시키거나, 무한 루프에 빠져서 영원히 정상 종료되지 않을 때 사용
    - 타입 추론: 개발자가 일일이 : string을 붙이지 않고 let name = "alice";라고만 적어도, TS 번역기가 알잘딱 해 안전망을 쳐주는 기능 // ??: 아 이 변수는 문자열이구나
  4. 복합 데이터: Array, Tuple, Enum
    - Array: number[] 또는 Array<number> 형태로, 배열 안에 들어갈 모든 데이터의 타입을 하나로 통일시킴
    - Tuple: 배열이지만, 각 자리마다 들어갈 타입과 총 길이가 정확히 고정된 배열이다
      // [number, string, string] 템플릿으로 정의하면, [1, "XSS", "HIGH"] 형태만 허용
    - Enum: 연관된 상수들의 집합
      - 문자열을 직접 하드코딩하면 오타가 날 수 있으므로, enum Severity { Low, MEDIUM, HIGH } 구조로 묶어둠
      - 문자열 열거형(String Enum): Enum은 0, 1, 2 같은 숫자로 변환되지만, 로그나 API 응답의 가독성을 위해
        **enum Severity { HIGH = "HIGH" }처럼 문자열 값을 명시적으로 할당하는 방식을 권장한다**
  5. 객체의 뼈대: Interface와 Class
    - Nest.js에서 데이터를 주고받을 때 객체의 형태를 규격화하는 아키텍처
    - Interface: 객체가 반드시 가저야 할 속성들의 껍데기를 정의한다
      - readonly: 한 번 값이 들어가면 수정할 수 없다 // readonly id: number;
      - ?(선택적 속성): 값이 있을 수도 있고 없을 수도 있음을 나타낸다 // description?: string;
    - 클래스와 접근 제어자
      - 객체 지향 설계를 위해 속성의 접근 범위를 제어한다
      - public: 어디서든 접근 가능함
      - private: 클래스 내부에서만 접근함
      - protected: 상속받은 자식 클래스까지만 허용함
    - implements 키워드: 클래스가 특정 인터페이스의 규격을 완벽하게 따르도록 컴파일 단계에서 강제하는 강력한 계약 역할을 한다
  6. Generics, <T>
    - 어떤 함수가 숫자 배열, 문자열 배열 등 여러 타입을 모두 처리하게 만들고 싶을 때 any를 쓰면 똥망
    - 이를 해결하는게 제네릭
    - 구조 원리
      - 함수나 인터페이스 이름 뒤에 <T>(Type의 약자)를 붙인다
      - 나중에 이 함수를 호출할 때, 그때 가서 어떤 타입인지 결정해서 템플릿처럼 끼워 넣겠다라는 뜻이다
      - Ex: function getFirst<T>(arr: T[]): T라고 설계해 두면, 문자열 배열을 넣었을 땐 T가 일괄적으로 string으로 변하고, 숫자를 넣으면 number로 작동한다
    - 제약 조건 - extends
      - <T>는 너무 자유로워서 어떤 타입이든 들어올 수 있다
      - 만약 데이터에 반드시 id값이 있어야만 로직이 돌아간다면, interface HasId { id: number; }를 만든 뒤,
        <T extends HasId>라고 제한을 걸 수 있다
      - 이래하면, T는 뭔지 모르지만, 최소한 id 속성은 가진 놈만 들어올 수 있다고 강제할 수 있다
