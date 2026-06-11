# 10단원: Fuzzing
## Page 1-2: 표지 및 목차
- 주제: 퍼징(Fuzzing)의 원리, 카테고리, 파이프라인, 최적화 및 스케일링 기법.

## Page 3: 프로그램 분석 개요 (Program Analysis: Overview)
- 프로그램 분석 기법은 크게 **정적 분석(Static)**, **동적 분석(Dynamic)**, **하이브리드 분석(Hybrid)**으로 나뉩니다.

## Page 4: 정적 분석 (Static Analysis)
- 프로그램을 실행하지 않고 코드의 구조와 데이터 흐름을 검사하는 기법.
- 대표적 기법: 제어/데이터 흐름 분석, 추상 해석, **기호 실행(Symbolic execution)**, **정적 테인트 분석(Static taint analysis)**.
- 한계점: 런타임 동작을 관찰할 수 없으며 **오탐(False positive)**이 발생할 수 있음.

## Page 5: 동적 분석 (Dynamic Analysis)
- 프로그램이 **실행되는 동안** 실제 경로, 메모리 사용, CPU 상태를 관찰하는 기법.
- 대표적 기법: **디버깅(Debugging)**, **퍼징(Fuzzing)**, **동적 테인트 분석(Dynamic taint analysis)**, **새니타이저(Sanitizer)**.
- 한계점: 제공된 입력값에 의해 트리거된 경로만 탐색 가능함.

## Page 6: 하이브리드 분석 (Hybrid Analysis)
- 정적 분석과 동적 분석의 장점을 결합한 방식.
- **콘콜릭 실행 (Concolic execution)**: 구체적(Concrete) 실행과 기호(Symbolic) 실행을 결합하여, 무작위 퍼징으로 도달하기 힘든 복잡한 분기 조건을 SMT 솔버를 이용해 돌파함.

## Page 7: 분석 기법 비교 (Comparison)
- **정적 분석**: 빠르고 전체 경로 커버리지가 높으나 오탐이 많음.
- **동적 분석**: 실행 속도는 중간이며 커버리지는 낮으나 오탐이 적음.
- **하이브리드 분석**: 느리지만 높은 커버리지와 낮은 오탐을 보임.

## Page 8: 수동 분석의 한계 (Limitations of Manual Analysis)
- 보안 전문가의 수작업 분석은 수천만 줄에 달하는 현대 소프트웨어의 규모와 복잡성을 감당할 수 없으며 모든 입력 조합을 테스트할 수 없음.

## Page 9: 스케일 문제 (The Scale Problem)
- 입력값의 공간(Input space)은 크기에 따라 기하급수적으로 증가하며, 실제 **버그(Bug)**와 **취약점(Vulnerability)**은 전체 공간 중 아주 작은 부분에만 존재함.

## Page 10: 퍼징이란? (What is Fuzzing?)
- **퍼징(Fuzzing)**: 자동으로 수많은 기형적(Malformed)이거나 무작위인 입력값을 생성해 타겟에 주입하고, **크래시(Crash)**나 **행(Hang)**과 같은 비정상 행위를 모니터링하는 **자동화된 소프트웨어 테스트 기법**.

## Page 11: 퍼징의 역사 (History of Fuzzing)
- 1990년, Barton Miller가 모뎀의 무작위 노이즈에 의해 Unix 프로그램들이 크래시나는 것을 관찰하고 최초의 자동화된 퍼징 툴을 개발함.

## Page 12: 도전 과제와 한계 (Challenges and Limitations)
- **커버리지 장벽 (Coverage wall)**: 깊게 중첩된 경로나 매직 밸류(Magic value) 검사를 무작위 변이만으로 통과하기 어려움.
- **오라클 문제 (Oracle problem)**: 논리 오류나 정보 유출처럼 크래시를 동반하지 않는(Silent) 버그는 퍼저가 탐지하기 어려움. (새니타이저 도입 필요)

## Page 13: 퍼징 카테고리 (Fuzzing Categories)
- 타겟 프로그램에 대한 정보량에 따라 **블랙박스(Black-box)**, **그레이박스(Grey-box)**, **화이트박스(White-box)** 퍼징으로 분류됨.

## Page 14: 블랙박스 퍼징 (Black-box Fuzzing)
- 내부 정보를 전혀 사용하지 않고 오직 출력 행위(크래시 여부)만 관찰.
- 무작위 생성, 변이(Mutation), 문법 기반(Generation) 방식이 있음.
- 소스코드 없이 적용하기 쉽지만 **피드백이 없어 탐색이 비효율적(Blind)**임.

## Page 15: 화이트박스 퍼징 (White-box Fuzzing)
- 소스코드 전체에 접근하여 가능한 모든 경로를 분석. (주로 기호/콘콜릭 실행 사용)
- 복잡한 조건을 통과할 수 있지만, 프로그램 규모가 커지면 경로가 기하급수적으로 늘어나는 **경로 폭발(Path explosion)** 문제와 솔버 오버헤드가 발생함.

## Page 16: 그레이박스 퍼징 (Grey-box Fuzzing)
- 가벼운 **인스트루멘테이션(Instrumentation)**을 삽입하여 코드 커버리지 피드백을 수집.
- 새로운 경로를 발견한 입력을 시드(Seed)로 저장하고 다시 변이시키는 **커버리지 기반 변이 루프(Coverage-guided mutation loop)**를 사용. (AFL, libFuzzer 등)

## Page 17: 퍼징 카테고리 비교 (Comparison)
- 현대 실무 보안 테스트에서는 속도와 커버리지의 밸런스가 좋은 **그레이박스 퍼징**이 가장 지배적으로 사용됨.

## Page 18: 커버리지가 중요한 이유 (Why Coverage Matters)
- 커버리지 피드백이 없으면 의미 없는 입력을 반복 생성하며 에너지를 낭비함.
- 커버리지는 퍼저에게 어떤 코드 영역이 실행되었는지 알려주어, 새로운 경로를 탐색하도록 유도하는 핵심 지표임.

## Page 19: 코드 커버리지의 종류 (Types of Code Coverage)
- 라인, 함수, 브랜치, 기본 블록, 엣지, 경로 커버리지 등이 있음.
- AFL/AFL++은 브랜치(Branch) 커버리지와 유사하면서 반복문의 횟수까지 구분할 수 있는 **엣지 커버리지(Edge coverage)**를 주요 피드백 신호로 사용함.

## Page 20: 브랜치 커버리지 예시 (Branch Coverage: Example)
- 모든 `if` 문의 참(True)과 거짓(False) 경로를 전부 도달해야 완벽한 브랜치 커버리지를 달성함. 피드백 루프를 통해 유망한 시드들을 점진적으로 변이시킴.

## Page 21: 그레이박스 퍼징 파이프라인 (Grey-box Fuzzing: Pipeline)
- 파이프라인 흐름: 초기 코퍼스 $\rightarrow$ **시드 큐(Seed Queue)** $\rightarrow$ 시드 선택 $\rightarrow$ **뮤테이터(Mutator)** $\rightarrow$ 테스트 케이스 실행 $\rightarrow$ **커버리지(Coverage)** 피드백 $\rightarrow$ 시드 업데이트.

## Page 22: 초기 코퍼스와 시드 큐 (Initial Corpus and Seed Queue)
- **초기 코퍼스 (Initial Corpus)**: 사용자가 제공하는 유효한 입력값 샘플. (다양할수록 좋음)
- **시드 큐 (Seed queue)**: 퍼징 중 새로운 엣지를 발견할 때마다 추가되는 시드들의 저장소.
- **afl-cmin**: 중복되는 엣지 커버리지를 가진 시드를 제거하여 코퍼스 크기를 최소화하는 도구.

## Page 23: 실행기 및 크래시 탐지 (Executor and Crash Detection)
- 타겟 프로그램을 실행하고 비정상 종료 시그널(**SIGSEGV**(메모리 에러), **SIGABRT**(Assertion 실패), **SIGFPE**(0으로 나누기))을 탐지함.

## Page 24: 시드 선택 알고리즘 (Seed Selection Algorithms)
- **Favored seed**: 특정 엣지를 커버하는 시드들 중 파일 크기가 가장 작은 시드를 우대(Favor)하여 더 많은 퍼징 에너지를 할당함 (AFL의 핵심 전략).

## Page 25: 전력 스케줄링 (Power Scheduling)
- **에너지 (Energy)**: 특정 시드에 부여할 변이(Mutation) 횟수.
- AFL은 실행 속도가 빠르고 크기가 작은 시드에 높은 에너지를 부여함. AFLFast는 희귀한 경로를 커버하는 시드에 기하급수적으로 에너지를 부여함.

## Page 26: AFL++의 스케줄링 전략 (Power Scheduling Strategies in AFL++)
- **Explore**: 희귀한 경로를 발견하는 것을 최우선으로 하여 다양성 극대화.
- **Exploit**: 크래시를 유발했던 시드에 집중하여 신뢰성 향상.
- 스케줄 옵션: fast, coe, mmopt 등.

## Page 27: 뮤테이션 개요 (Mutation: Overview)
- **뮤테이션 (Mutation)**: 기존 시드를 변형하여 새로운 테스트 케이스를 생성하는 과정.
- 비트/바이트 플리핑, 산술 연산, 딕셔너리 삽입, Havoc/Splice, 구조 인식 변이 등이 있음.

## Page 28: 비트 및 바이트 플리핑 (Bit and Byte Flipping)
- 슬라이딩 윈도우 방식으로 1~32 비트 너비를 순차적으로 반전시킴. 항상 같은 결과를 내는 **결정론적(Deterministic)** 단계임.

## Page 29: 산술 및 딕셔너리 뮤테이션 (Arithmetic and Dictionary Mutations)
- **산술 변이**: 정수 필드에 작은 값($\pm35$)을 더하거나 뺌.
- **딕셔너리 변이**: 정수 오버플로우를 유발하기 쉬운 경곗값(0, 255, -1, 0x7FFFFFFF 등)이나 특정 매직 바이트를 삽입함.

## Page 30: Havoc 및 Splice (Havoc and Splice)
- **Havoc**: 여러 변이 기법들을 무작위로 복합 적용하여 대혼란을 줌.
- **Splice**: 두 개의 서로 다른 시드를 무작위 지점에서 자른 후 결합(Concatenate)하여 구조적 다양성을 부여함.

## Page 31: 구조 인식 뮤테이션 (Structure-aware Mutation)
- JSON/XML 등 엄격한 포맷의 경우, 단순 바이트 변이는 구문 오류로 일찍 기각됨.
- 이를 해결하기 위해 문맥 자유 문법(CFG)을 활용하는 **문법 기반 퍼징(Grammar-based fuzzing)**이나 AFL++의 `libAFLCustomMutator` (커스텀 뮤테이터)를 활용함.

## Page 32: 인스트루멘테이션 개요 (Instrumentation: Overview)
- **인스트루멘테이션 (Instrumentation)**: 타겟 프로그램의 행위(실행된 엣지)를 관찰하기 위해 코드를 삽입하는 것.
- 컴파일 타임(소스코드 필요, 오버헤드 적음) 방식과 동적(소스코드 불필요, 오버헤드 큼) 방식으로 나뉨.

## Page 33: 컴파일 타임 인스트루멘테이션 (Compile-time Instrumentation)
- 컴파일 과정(LLVM pass 등)에서 컴파일러가 직접 각 기본 블록(Basic block) 간의 전환을 기록하는 코드를 삽입함.

## Page 34: 동적 인스트루멘테이션: QEMU 모드 (Dynamic Instrumentation: QEMU Mode)
- **QEMU 모드**: 소스코드가 없는 닫힌 소스(Closed-source)나 아키텍처가 다른 바이너리(ARM, MIPS 등)를 퍼징할 때 QEMU 에뮬레이터를 이용해 런타임에 동적으로 코드를 삽입함.

## Page 35: 포크서버 (Forkserver)
- 테스트 케이스마다 `execve()`를 호출하여 새로운 프로세스를 띄우는 것(초기화 비용)은 매우 무거움.
- **포크서버 (Forkserver)**: 타겟을 한 번만 초기화한 뒤, 퍼저의 신호를 받아 상태가 이미 초기화된 프로세스를 **`fork()`**하여 오버헤드를 대폭 줄이는 기법.

## Page 36: 지연 포크서버 (Deferred Forkserver)
- 무거운 초기화 작업(설정 파일 로딩, DB 검증 등)이 끝난 직후 시점에 `__AFL_INIT()` 매크로를 삽입해 `fork()` 시점을 늦춤으로써 초기화 오버헤드를 완벽히 건너뛰는 최적화 기법.

## Page 37-38: 영구 모드 (Persistent Mode)
- `__AFL_LOOP()`를 사용하여 **단일 프로세스 내부에서 반복문**을 돌며 수많은 입력값을 연속적으로 처리하는 방식.
- `fork()` 오버헤드마저 제거하여 일반 포크서버 대비 압도적인 처리 속도(50x 이상)를 보이지만, 전역 상태가 누적(State accumulation)될 위험이 있어 수동으로 상태를 초기화해야 함.

## Page 39: 실행 최적화 비교 (Execution Optimization: Comparison)
- 실행 속도: 기본 `execve` (1x) < 포크서버 (5x) < 지연 포크서버 (10x) < **영구 모드(Persistent mode)** (50x 이상). 초당 실행 횟수가 높을수록 버그 발견 확률이 증가함.

## Page 40: 병렬 퍼징 (Parallel Fuzzing)
- 멀티 코어 환경에서 여러 퍼저 인스턴스를 동시에 실행시키고, 주기적으로 큐(Queue)를 동기화(Sync)하여 서로 발견한 유용한 시드를 공유하는 기법.

## Page 41: 입력값 최소화 (Input Minimization)
- 크래시를 유발하는 입력값 중 실제 크래시에 영향을 주지 않는 불필요한 바이트를 제거하여 크기를 줄이는 과정.
- **afl-tmin** 도구를 사용하며, 버그의 근본 원인(Root-cause) 분석과 후속 뮤테이션 효율을 높이는 데 필수적임.

## Page 42: 지향성 퍼징 개념 (Directed Fuzzing: Concept)
- 전체 코드 커버리지를 높이는 일반적인 그레이박스 퍼징과 달리, 패치된 함수나 알려진 크래시 위치 등 **특정 타겟 지점**에 도달하는 것을 최우선 목표로 하는 퍼징 기법.

## Page 43: AFLGo: 거리 기반 지향성 퍼징 (AFLGo)
- 타겟 지점까지의 **거리(Distance)**를 계산하여 거리가 짧은 시드에 더 많은 에너지를 부여함.
- **시뮬레이티드 어닐링 (Simulated annealing)** 스케줄링을 사용: 초기에는 커버리지를 넓히기 위해 광범위하게 탐색하고, 후기에는 타겟 근처의 시드들을 집중적으로 변이(Exploit)시킴.
