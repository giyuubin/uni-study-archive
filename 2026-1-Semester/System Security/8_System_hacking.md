# 8단원: System Hacking
## Page 1-2: 표지 및 목차
- 주제: 시스템 해킹 (pwntools, 스택 버퍼 오버플로우, 메모리 레이아웃, Ret2Libc, 시큐어 코딩 등).

## Page 3: pwntools의 필요성 (Why pwntools?)
- **pwntools**: 익스플로잇 개발을 위한 파이썬 라이브러리.
- 저수준의 네트워킹과 패킹 작업을 숨겨주어 편리함. 주요 기능으로 Tubes, 정수 패킹/언패킹, ELF 파서, 쉘코드 생성(**shellcraft**), GDB 연동 등이 있음.

## Page 4: pwntools 설치 및 보조 도구 (Installation)
- `pip`를 통해 설치하며 Python 3를 타겟으로 함.
- 보조 도구 1: **ROPgadget** (ELF 바이너리에서 사용 가능한 가젯을 찾음).
- 보조 도구 2: **one_gadget** (libc에서 쉘을 띄우는 명령어를 추출).

## Page 5: Tubes (프로세스 및 원격)
- **Tube**: 양방향 바이트 채널 추상화. 로컬 프로세스(`process()`)와 원격 서비스(`remote()`)에 동일한 API 사용.
- `recvuntil()`로 프롬프트와 동기화하고, 익스플로잇 성공 후 `interactive()`로 터미널 제어권을 가져옴.

## Page 6: 정수 패킹과 ELF 객체 (Packing Integers and ELF Object)
- **p64 / u64**: 아키텍처의 바이트 순서(리틀 엔디안 등)에 맞게 정수를 패킹/언패킹하는 헬퍼 함수.
- **ELF()**: 파일을 파싱하여 심볼(symbols), GOT, PLT 오프셋 등을 쉽게 추출(`elf.symbols['main']` 등).

## Page 7: context, shellcraft, gdb.attach
- **context**: 어셈블리를 위해 글로벌 아키텍처와 운영체제를 설정.
- **shellcraft**: 어셈블리 템플릿(쉘코드)을 자동 생성.
- **gdb.attach**: 실행 중인 Tube를 일시 정지하고 GDB를 연결하여 메모리를 동적으로 디버깅할 수 있게 함.

## Page 8: 익스플로잇 기본 구조 (Skeleton of an Exploit)
- 랩(Lab) 실습 원리: 로컬 바이너리를 실행하고, 메모리 릭(Leak)을 받아와 타겟 주소 오프셋을 계산한 뒤, 페이로드를 조작하여 전송하고 `interactive()`로 쉘을 획득하는 흐름.

## Page 9: cyclic 패턴을 이용한 오프셋 찾기 (Finding the Offset)
- **cyclic**: 위치마다 고유한 4바이트 태그를 삽입한 패턴 생성.
- 프로그램 크래시 시 덮어씌워진 RIP 값을 **cyclic_find()**에 넣으면 오버플로우 발생 지점의 정확한 **오프셋(Offset)**을 자동으로 계산할 수 있음.

## Page 10: 정수 표현 복습 (Integer Representation Review)
- C언어에서 정수는 고정 폭 바이너리 값을 가짐. `unsigned int`는 0부터 2^32-1까지 표현.
- **부호 없는 정수(Unsigned)** 연산은 범위를 넘으면 모듈로 연산으로 랩어라운드(wraps around)됨.
- 부호 있는 정수(Signed)의 오버플로우는 C 언어에서 **미정의 동작(Undefined behavior)**임.

## Page 11: 오버플로우와 언더플로우 경계 (Overflow and Underflow Boundary)
- 최대값을 넘겨 증가시키면 0으로 **오버플로우(Overflow)**.
- 0보다 작게 감소시키면 최대값으로 **언더플로우(Underflow)**.

## Page 12: 부호 있는 정수에서 부호 없는 정수로의 변환 (Signed-to-Unsigned Conversion)
- 메모리 복사 API(`memcpy` 등)는 인자로 부호 없는 **size_t**를 받음.
- 음수 값이 전달되면 조용히 **거대한 양수(huge value)**로 변환되므로, 하한(lower bound)과 상한(upper bound) 검사가 모두 필수적임.

## Page 13: 취약한 코드: 크기 검사 우회 (Size Check Bypass)
- 하한선(0 미만) 검사가 누락된 상황에서 사용자가 0을 입력하면 `size - 1` 연산이 -1이 되고, 이는 부호 없는 정수에서 최댓값(`0xFFFFFFFF`)으로 변환되어 **스택 버퍼 오버플로우(Stack BoF)**를 유발함.

## Page 14: 실습: 랩어라운드 트리거 (Practice: Trigger the Wrap)
- 랩(Lab) 실습 원리: 크기로 '0'을 입력해 언더플로우를 유발한 뒤, 버퍼 크기를 초과하는 대량의 문자열을 전송하여 반환 주소(RIP)를 장악하는 원리.

## Page 15: 스택 프레임 복습 (Stack Frame Revisited)
- 메모리 구조 하단부터: 로컬 버퍼 -> 스택 패딩 -> **Saved RBP** -> **Return address (반환 주소)** 순으로 위치.
- 버퍼를 넘어선 쓰기는 Saved RBP와 Return address를 모두 손상시킴.

## Page 16: 스택에서의 오버플로우 효과 (Overflow Effect on the Stack)
- 사용자 입력은 메모리 하위 주소에서 상위 주소로(downward) 채워짐.
- 잉여 데이터가 제어 흐름(Control flow)을 조작하여 공격자가 원하는 주소로 `ret` 명령을 실행하게 함.

## Page 17: 흔히 취약한 함수들 (Commonly Vulnerable Functions)
- **gets**, **strcpy**, **strcat**, **sprintf**, **scanf("%s")**: 길이 검사(bound check)를 하지 않거나 널 바이트(NUL) 및 공백 전까지 계속 입력받아 버퍼 오버플로우에 매우 취약함.
- 사용자 입력 크기를 제어하는 **read()** 함수라도 길이를 공격자가 통제할 수 있다면 위험함.

## Page 18: 오프셋 결정 방법 (Determining the Offset)
- **정적(Static) 방식**: 디스어셈블리 코드를 분석하여 변수와 RBP 간의 거리를 계산.
- **동적(Dynamic) 방식**: GDB에서 `cyclic` 패턴을 주입한 뒤, 크래시가 난 RIP 값을 확인해 거리를 도출.

## Page 19: 반환 주소 덮어쓰기 개념 (Idea of Return Address Overwrite)
- 메모리에 쉘코드 주입 없이 프로그램 내부에 이미 존재하는 특정 함수(예: `win()`, `print_flag()`)로 **제어 흐름을 우회(Redirect)**시키는 가장 단순한 익스플로잇 기법.

## Page 20: 대상 함수 발견 실습 (Target Function Discovery)
- 랩(Lab) 실습 원리: 컴파일된 바이너리를 `objdump -d` 명령어로 분석하여 내부 숨겨진 함수(`print_flag`)의 절대 주소를 찾아내는 원리.

## Page 21: 페이로드 레이아웃 (Payload Layout)
- 페이로드 구성: **Junk** (버퍼 + 패딩 크기만큼 더미 데이터 채움) + **Junk** (Saved RBP 덮어쓰기용 8바이트) + **타겟 함수 주소** (Return Address 위치에 덮어씀).

## Page 22: 실습: print_flag 호출 (Practice: Calling print_flag)
- 랩(Lab) 실습 원리: pwntools의 `elf.symbols`를 이용해 타겟 함수 주소를 쉽게 얻고, 구한 오프셋만큼 버퍼를 덮어쓴 뒤 `p64()`로 패킹한 주소를 이어붙여 전송.

## Page 23: 스택 정렬 함정 (Stack Alignment Pitfall - movaps)
- AMD64 호출 규약(ABI)은 함수 호출 시 **RSP가 16바이트 정렬(Alignment)**되어 있어야 함.
- libc 내부에서 쓰이는 `movaps` 명령어는 정렬이 안 맞으면 SIGSEGV 크래시를 유발함.
- 해결책: 타겟 함수 실행 직전에 **ret 가젯(gadget)**을 한 번 더 실행해 스택 포인터를 8바이트 이동시켜 정렬을 맞춤.

## Page 24: 쉘코드란? (What Is Shellcode?)
- **쉘코드 (Shellcode)**: 대화형 쉘을 실행하기 위해 메모리에 직접 주입하여 실행시키는 짧은 **기계어 코드(Machine code)**.
- 실행 가능한 메모리 공간이 필요하며, **NX(Non-Executable) 보호기법이 비활성화(`-z execstack`)**되어 있어야 동작함.

## Page 25: x86-64의 execve 시스템 콜 (execve Syscall on x86-64)
- 쉘 획득을 위해 주로 `execve("/bin/sh", NULL, NULL)` 시스템 콜을 사용.
- **rax**에 시스템 콜 번호 59를 넣고, **rdi**에 문자열 포인터, **rsi**와 **rdx**를 0으로 초기화한 후 `syscall` 호출.

## Page 26: shellcraft를 이용한 쉘코드 생성 (Generating Shellcode)
- pwntools의 `shellcraft.amd64.linux.sh()`를 이용해 쉽게 쉘코드를 자동 생성할 수 있음.
- strcpy 계열 함수는 널 바이트(NUL)에서 복사가 끊기지만, `read()`는 널 바이트 제약이 없음.

## Page 27: 쉘코드 배치 위치 (Where to Place the Shellcode)
- 위치 1: 크기가 충분하다면 **버퍼 내부(Inside the buffer)**.
- 위치 2: 공간이 좁다면 **반환 주소 뒤(After the return address)**.

## Page 28: NOP 슬레드 (NOP Sled)
- ASLR 환경 등으로 인해 정확한 쉘코드 주소를 예측하기 어려울 때 사용하는 기법.
- 쉘코드 앞에 다량의 **NOP(`0x90`)** 명령어를 깔아두어, 실행 흐름이 아무 NOP 위치에 떨어지더라도 미끄러지듯(slides) 실제 쉘코드로 도달하게 함.

## Page 29: 실습: 취약한 프로그램 (Practice: Vulnerable Program)
- 랩(Lab) 실습 원리: 취약한 프로그램이 버퍼의 주소를 화면에 출력해주어(메모리 릭), 공격자가 쉘코드가 삽입된 버퍼의 절대 주소를 안정적으로 계산할 수 있게 함.

## Page 30: 실습: 익스플로잇 스크립트 (Practice: Exploit Script)
- 랩(Lab) 실습 원리: 유출된 주소를 파싱하고 `[더미 버퍼 + RBP] + [타겟 주소] + [NOP Sled + 쉘코드]` 형태로 페이로드를 전송하여 쉘코드로 흐름을 넘김.

## Page 31: 런타임 프로세스 메모리 맵 (Process Memory Map)
- **ASLR(Address Space Layout Randomization)**에 의해 실행마다 Stack과 Libc의 시작 주소(Base)가 무작위로 변경됨.
- 코드 영역은 읽기/실행(RX) 권한만 존재함.

## Page 32: /proc/PID/maps 검사 (Inspecting /proc/PID/maps)
- `/proc/PID/maps`를 통해 메모리 매핑 상태 확인 가능.
- libc의 기본 주소(Base address)를 알아내기만 하면 단 한 번의 **릭(Leak)**으로 라이브러리 내 모든 함수의 실제 주소를 계산할 수 있음.

## Page 33: Libc 오프셋이 고정된 이유 (Why Libc Offsets Are Fixed)
- libc는 단일 공유 객체(Shared object)이므로 내부 심볼(함수)들 간의 거리는 항상 고정되어 있음.
- **ASLR**은 이 라이브러리가 맵핑되는 전체 덩어리의 **시작 주소(Base)**만 랜덤화할 뿐임.

## Page 34: Libc를 위한 pwntools ELF (pwntools ELF for Libc)
- `libc.symbols['system']`이나 `libc.search(b'/bin/sh')`을 통해 시스템 라이브러리 내부 함수나 특정 문자열의 고정 **오프셋(Offset)**을 추출함.

## Page 35: Libc 베이스 주소 계산 (Computing the Libc Base)
- **Libc Base 주소 = 유출된 런타임 함수 주소 - 해당 함수의 libc 내부 오프셋.**
- pwntools에서 `libc.address` 속성을 갱신하면 다른 모든 심볼 주소가 자동으로 절대 주소로 재배치됨.

## Page 36: 메모리 릭(Leak)이 필요한 이유 (Why We Need a Leak)
- ASLR 방어기법이 적용된 실제 시스템에서는 실행 시점의 메모리 주소를 알 수 없음.
- 따라서 런타임에 동적으로 주소 정보 하나를 빼내는 **메모리 릭(Memory Leak)** 과정이 필수적임.

## Page 37: puts와 GOT를 이용한 릭 (Leaking via puts and GOT)
- **GOT (Global Offset Table)**: 외부 라이브러리 함수의 실제 런타임 주소를 저장하는 테이블.
- `puts(puts@got)` 형태로 호출하면 GOT 테이블에 적힌 실제 `puts` 함수의 libc 주소를 화면에 출력해줌.

## Page 38: 2단계 익스플로잇 패턴 (Two-stage Exploit Pattern)
- **Stage 1 (Leak):** `puts`를 이용해 libc 주소를 유출시킨 뒤, 프로그램 흐름을 `main()`으로 다시 되돌림.
- **Stage 2 (Exploit):** `main()`이 다시 실행되면 계산된 libc 베이스 주소를 바탕으로 실제 공격(쉘 획득) 페이로드를 전송.

## Page 39: Return-to-Libc (ret2libc) 개념 (Idea of Return-to-Libc)
- 스택 메모리에 실행 권한을 없애는 **NX(Non-Executable stack)** 방어기법을 우회하기 위한 공격.
- 쉘코드 주입 없이 이미 실행 권한이 있는 **libc 영역의 코드(`system("/bin/sh")`)를 재사용**하여 공격함.

## Page 40: x86-64 호출 규약 (x86-64 Calling Convention)
- 64비트 리눅스의 함수 인자 전달 순서 레지스터: **rdi, rsi, rdx, rcx, r8, r9**.
- `system()` 함수의 첫 번째 인자로 문자열 주소를 넣기 위해 공격자는 제어권 이동 전에 **rdi** 레지스터를 통제해야 함.

## Page 41: 가젯이란? (What Is a Gadget?)
- **가젯 (Gadget)**: 레지스터 값을 조작하고 마지막에 `ret` 명령어로 끝나는 아주 짧은 기계어 코드 조각.
- 가젯들을 사슬처럼 연결하는 기법을 **ROP (Return-Oriented Programming)**라고 함. ret2libc의 필수 최소 가젯은 `pop rdi; ret`임.

## Page 42: ROPgadget으로 가젯 찾기 (Finding Gadgets)
- 터미널에서 `ROPgadget` 툴을 사용하거나 pwntools 스크립트 내에서 `ROP(elf).find_gadget(...)` 메서드를 사용해 가젯의 주소를 찾음.

## Page 43: ret2libc 페이로드 레이아웃 (ret2libc Payload Layout)
- 구성: **더미(버퍼+RBP)** + **[pop rdi; ret] 가젯 주소** + **"/bin/sh" 문자열 주소** + **system 함수 주소**.

## Page 44: 실습: 대상 프로그램 (Practice: Target Program)
- 랩(Lab) 실습 원리: NX가 켜진 상태에서 버퍼 오버플로우를 발생시켜 ret2libc 공격을 연습하기 위해 의도적으로 작성된 타겟 C 코드 분석.

## Page 45-46: 실습: 1단계 - Libc 릭 (Practice: Stage 1)
- 랩(Lab) 실습 원리: `pop_rdi` 가젯을 이용해 `puts@got` 주소를 `puts@plt`의 인자로 넘겨 호출한 뒤, 다시 `main`으로 되돌아오게 하여 ASLR에 의해 랜덤화된 libc 기본 주소를 계산.

## Page 47: 실습: 2단계 - 쉘 실행 (Practice: Stage 2)
- 랩(Lab) 실습 원리: 재실행된 `main` 함수에서 다시 오버플로우를 발생시켜 `pop_rdi` 가젯 -> 계산된 `"/bin/sh"` 주소 -> 스택 정렬용 `ret` 가젯 -> `system` 함수 순서로 제어 흐름을 덮어써 쉘 획득.

## Page 48: 대안: one_gadget (Alternative: one_gadget)
- `pop rdi; ret` 가젯을 찾을 수 없을 때 활용하는 강력한 대안.
- libc 내부에서 단일 점프로 곧바로 쉘을 실행(`execve`)해버리는 특정 주소(**one_gadget**)를 찾음. 단, 특정 레지스터가 NULL이어야 하는 등의 **제약조건(Constraints)**을 만족해야 작동함.

## Page 49: 시큐어 코딩 - 경계 검사 (Bounds Checking First)
- 목적지 버퍼의 크기를 항상 인지하고 명시적으로 복사 함수에 전달할 것.
- 신뢰할 수 없는 사용자 입력 길이(길이가 음수이거나 최대를 넘는지)를 반드시 연산 전에 검증할 것.

## Page 50: 시큐어 코딩 - 안전한 문자열 API (Safer String API Replacements)
- 위험한 함수(`gets`, `strcpy`, `sprintf`, `scanf`) 대신, 최대 버퍼 길이를 제한하는 안전한 함수인 **fgets**, **strncpy/strlcpy**, **strncat/strlcat**, **snprintf** 등을 사용해야 함.

## Page 51: 시큐어 코딩 - 안전한 정수 연산 (Safe Integer Arithmetic)
- 크기 관련 변수는 일관되게 부호 없는 타입(**size_t**)을 사용할 것.
- GCC/Clang에서 지원하는 오버플로우 체크 내장 함수(`__builtin_mul_overflow` 등)를 적극 사용하여 연산 전 오버플로우를 방지함.

## Page 52: 시큐어 코딩 - 신뢰할 수 없는 입력 검증 (Validate Untrusted Input)
- 외부 데이터는 모두 적대적인 것으로 간주.
- 거부 목록(deny-list)보다 허용 목록(**allow-list**) 기반의 검증을 적용하고, 파싱(parsing) 전에 비정상 입력은 조기 차단해야 함.

## Page 53: 시큐어 코딩 - 도구 지원 (Tooling Support)
- 컴파일러 경고 켜기 (`-Wall`, `-Wextra` 등).
- 런타임 검사 도구(Sanitizers): 메모리 릭 탐지(**AddressSanitizer**), 정수 오버플로우 탐지(**UndefinedBehaviorSanitizer**).
- 지속적 통합(CI) 환경에 퍼징(Fuzzing - **libFuzzer**, **AFL++**) 도구 도입.
