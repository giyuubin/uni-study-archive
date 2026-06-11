# 11단원: Sanitizer
## Page 1-2: 표지 및 목차
- 주제: 새니타이저(Sanitizer)의 개념, 종류(ASAN, UBSAN 등) 및 퍼징(Fuzzing)에서의 활용.

## Page 3: 새니타이저란? (What is a Sanitizer?)
- **새니타이저 (Sanitizer)**: 프로그램 내부에 내장되어 **컴파일 타임(Compile time)**에 추가적인 검사 코드로 삽입되는 **버그 탐지 기법(Bug-detection technique)**.
- 수동 코드 리뷰에서 놓치기 쉬운 런타임 버그나, 특정 입력에서만 발생하는 **조용한 메모리 에러(Silent memory errors)**를 명확한 에러로 노출시키고 정확한 위치와 콜 스택을 제공함.

## Page 4: 컴파일 타임 인스트루멘테이션 vs 런타임 검사 (Compile-time Instrumentation vs Runtime Checks)
- 컴파일러가 컴파일 중에 소스 코드를 **인스트루멘테이션(Instrumentation)**하여 검사 코드를 바이너리 내부에 삽입함.
- 별도의 도구 없이 프로그램과 함께 실행되며, 매 접근마다 런타임 검사를 수행하므로 실행 속도 저하 및 추가 메모리 비용이 발생함.

## Page 5: 새니타이저의 종류 (The Family of Sanitizers)
- **ASAN (Address)**: 버퍼 오버플로우, UAF(Use-after-free) 탐지.
- **UBSAN (Undefined Behavior)**: 정수 오버플로우, Null 포인터 역참조 등 탐지.
- **MSAN (Memory)**: 초기화되지 않은 메모리 사용 탐지.
- **TSAN (Thread)**: 스레드 간의 데이터 경쟁(Data races) 탐지.
- **LSAN (Leak)**: 메모리 누수 탐지.
- 퍼징에서는 **ASAN**과 **UBSAN**이 가장 널리 사용됨.

## Page 6: Address Sanitizer (ASAN)
- C/C++을 위한 빠르고 강력한 메모리 에러 탐지기.
- 다른 도구에 비해 오버헤드가 적음 (약 **2x 속도 저하**).
- **탐지 가능한 버그**: 힙/스택/전역 버퍼 오버플로우, **UAF(Use-After-Free)**, UAR(Use-After-Return), Double-free, 메모리 누수.

## Page 7: ASAN의 3가지 핵심 개념 (ASAN: Three Core Concepts)
- **레드존 (Redzone)**: 각 객체(Object) 주변에 배치되는 접근 불가 보호 구역.
- **섀도우 메모리 (Shadow memory)**: 실제 메모리의 매 8바이트 상태를 설명하는 메타데이터.
- **인스트루멘테이션 (Instrumentation)**: 메모리에 접근하기 전에 유효성을 검사하는 런타임 검사 로직.

## Page 8: ASAN: 레드존 (ASAN: Redzone)
- ASAN은 모든 객체를 **오염된(Poisoned) 레드존**으로 둘러쌈.
- 오버플로우가 발생하여 레드존에 접근하게 되면 이를 즉시 거부하고 에러를 발생시킴.

## Page 9: ASAN: 섀도우 메모리 (ASAN: Shadow Memory)
- 1바이트의 **섀도우 바이트(Shadow byte)**가 실제 애플리케이션 메모리 **8바이트**의 상태를 나타냄.
- 결과적으로 전체 주소 공간의 **1/8**을 섀도우 메모리로 사용함.

## Page 10: ASAN: 섀도우 바이트 값 (ASAN: Shadow Byte Values)
- **0x00**: 8바이트 전체가 접근 가능(유효함).
- **k (1~7)**: 처음 k바이트만 접근 가능.
- **음수 (Negative values)**: 오염된(Poisoned) 메모리 영역. (예: `0xfa`=힙 레드존, `0xf1/0xf3`=스택 레드존, `0xfd`=해제된 힙 메모리).

## Page 11: ASAN: 섀도우 메모리와 레드존의 결합 (Shadow Memory + Redzone Together)
- 실제 메모리에 배치된 레드존의 상태가 섀도우 메모리에 그대로 반영됨.
- ASAN은 섀도우 메모리를 한 번 조회하는 것만으로 해당 주소의 안전성 여부를 파악함.

## Page 12: ASAN: 주소 매핑 (Address to Shadow Mapping)
- 실제 주소를 섀도우 바이트 주소로 변환하는 공식: **`(addr >> 3) + offset`** (주소를 8로 나누고 오프셋을 더함).
- 연산이 매우 간단하여 ASAN의 검사 속도를 빠르게 유지할 수 있음.

## Page 13: ASAN: 인스트루멘테이션 흐름 (ASAN: Instrumentation Flow)
- 컴파일러가 메모리 할당 및 접근 코드를 재작성함.
- 할당 시 섀도우 메모리를 **오염(Poison)**시키고, 접근할 때 유효성을 검사하며, 해제할 때 다시 상태를 업데이트함.

## Page 14: ASAN: 인스트루멘테이션 예제 (ASAN: Instrumented Example)
- 배열의 범위를 벗어난 쓰기(OOB Write) 시도 시, 값을 쓰기 **직전에** 섀도우 메모리를 검사하여 레드존(`0xfa`)임을 확인하고 에러를 보고(`ReportError`)함.

## Page 15: ASAN: 탐지 규칙 (ASAN: Detection Rule)
- 접근 전 섀도우 바이트를 읽어 **음수(오염됨)**면 접근을 거부하고 프로그램을 **중단(Abort)**함.
- **0(오염되지 않음)**이면 접근을 허용함.
- 이 단일 규칙 하나로 버퍼 오버플로우, UAF 등 다양한 메모리 버그를 모두 잡아냄.

## Page 16: ASAN: 에러 리포트 읽기 (Reading the Error Report)
- 에러 리포트에는 버그 종류(`stack-buffer-overflow`), 발생 주소, 콜 스택(Call stack), 그리고 문제 주소 주변의 **섀도우 바이트 상태(Shadow bytes)**가 상세히 출력됨.

## Page 17: 미정의 동작이란? (What is Undefined Behavior?)
- **미정의 동작 (Undefined Behavior, UB)**: C 표준에서 결과를 정의하지 않은 연산 (예: 부호 있는 정수 오버플로우).
- 컴파일러마다 결과가 다르게 나타날 수 있어 매우 위험하며, **UBSAN**이 이를 런타임에 잡아냄.

## Page 18: UBSAN: 탐지 가능한 버그 (UBSAN: Detectable Bugs)
- 부호 있는/없는 **정수 오버플로우(Integer overflow)**.
- **0으로 나누기 (Division by zero)**.
- **Null 포인터 역참조 (Null pointer dereference)**.
- 배열 인덱스 초과 및 잘못된 형변환(Invalid casts).

## Page 19: UBSAN: 탐지 규칙 (UBSAN: Detection Rules)
- ASAN과 달리, UBSAN은 각 UB 유형마다 **구체적인 검사 규칙(Rule)**을 가짐.
- 덧셈은 결과의 부호 확인, 곱셈은 정수 범위 초과 확인 등 연산 직전에 규칙을 평가함.

## Page 20: UBSAN: 인스트루멘테이션 방법 (UBSAN: How It Instruments)
- 위험한 연산이 실행되기 **바로 앞**에 조건문(`__builtin_add_overflow_p` 등)을 삽입하여 규칙 위반 시 핸들러를 호출해 파일과 라인 번호를 출력함.

## Page 21: Memory Sanitizer (MSAN)
- **초기화되지 않은 메모리(Uninitialized memory)**의 읽기를 탐지.
- 모든 값에 대해 "초기화 여부"를 추적하는 비트를 유지함. 프로그램 전체가 MSAN으로 빌드되어야 정상 동작함.

## Page 22: Thread Sanitizer (TSAN)
- 동시 실행되는 스레드 간의 **데이터 경쟁(Data races)**을 탐지.
- 동기화되지 않은 상태에서 한 스레드라도 쓰기(Write) 연산을 수행할 때 발생하며, 재현하기 힘든 메모리 오염의 원인이 됨.

## Page 23: Leak Sanitizer (LSAN) 및 성능 비교 (LSAN and Comparison)
- **LSAN**: 프로그램 종료 시 해제되지 않은 힙 블록(**메모리 누수**)을 보고하며, 보통 ASAN에 내장되어 함께 동작함.
- **성능 오버헤드**: UBSAN(매우 적음) < ASAN(약 2x) < MSAN(약 3x) < TSAN(5x~15x, 매우 높음).
- ASAN과 MSAN은 메모리 매핑 충돌로 **동시에 사용할 수 없음**.

## Page 24: 퍼징에 새니타이저가 필요한 이유 (Why Fuzzing Needs Sanitizers)
- 퍼저는 타겟이 가시적으로 **크래시(Crash)**를 일으켜야만 버그를 인지함.
- 새니타이저는 눈에 띄지 않는 **조용한 메모리 에러(Silent bug)**를 강제 종료(Abort) 시그널로 바꾸어 주어, 퍼저가 이를 유효한 크래시로 기록할 수 있게 만듦.

## Page 25: AFL++와 새니타이저 통합 (Integrating Sanitizers with AFL++)
- 환경 변수(`AFL_USE_ASAN=1`, `AFL_USE_UBSAN=1`)를 설정하면 컴파일러 래퍼(Wrapper)가 자동으로 관련 플래그를 추가함.
- 메모리 오염(ASAN)과 미정의 동작(UBSAN)을 함께 잡음으로써 퍼저가 찾아내는 버그의 수를 비약적으로 늘림.

## Page 26: 캠페인을 위한 새니타이저 선택 (Choosing Sanitizers for a Campaign)
- 새니타이저는 퍼징의 **초당 실행 횟수(Throughput)**를 떨어뜨리므로 버그 탐지율과 속도 간의 균형이 필요함.
- 일반적인 전략: **새니타이저 없이 최고 속도**로 퍼징하여 크래시를 모은 뒤, 나중에 **ASAN을 켜고 재현(Replay)**하여 분석함.

## Page 27: 새니타이저의 한계 (Limitations of Sanitizers)
- 퍼저나 테스트 케이스가 **실제로 도달(Executed)**한 경로에 있는 버그만 찾을 수 있음.
- 런타임 및 메모리 오버헤드가 너무 커서 실제 운영(Production) 환경에 배포할 수 없음.
- 완전한 무결성 증명이 아닌 **강력한 탐지기(Detectors)** 역할을 함.
