# 9단원: Process Protection
## Page 1-2: 표지 및 목차
- 주제: 프로세스 보호 기법 (Process Protection) 및 우회 방법.

## Page 3: 메모리 보호의 개념 (What is Memory Protection?)
- **메모리 보호 (Memory Protection)**: 권한 없는 접근이나 수정으로부터 메모리 내 데이터를 보호하고 프로그램 제어 흐름의 하이재킹을 방지함.
- 절대적인 방어벽이 아닌 공격의 비용과 복잡성을 높이는 **강화 기법(Hardening techniques)**임.

## Page 4: 보호 기법 개요 (Overview of Protection Mechanisms)
- **디버그 심볼 스트리핑 (Debug symbol stripping)**: 리버스 엔지니어링 방해.
- **스택 카나리 (Stack canary)**: 스택 버퍼 오버플로우 탐지.
- **DEP (NX bit)**: 쉘코드 실행 방지.
- **ASLR**: 메모리 주소 무작위화.
- **PIE / PIC**: 실행 파일 베이스 주소 무작위화.

## Page 5-6: 디버그 심볼과 섹션 검사 (Debug Symbols)
- **디버그 심볼 (Debug Symbols)**: 컴파일 중 바이너리에 삽입되는 메타데이터 (함수명, 변수명, 소스 경로 등).
- `.symtab`, `.strtab`, `.debug_info` 등의 ELF 섹션에 저장되며 실행에는 불필요함.
- `readelf`, `nm` 명령어로 심볼 확인 가능.

## Page 7-8: 리버스 엔지니어링에 미치는 영향 및 스트리핑 방법 (Impact and Stripping)
- 심볼이 없으면 함수명이 자동 생성(예: `FUN_00401149`)되어 **리버스 엔지니어링 난이도가 크게 상승**함.
- 컴파일 시 `-s` 플래그를 사용하거나, 컴파일 후 `strip` 명령어를 사용하여 심볼을 제거할 수 있음.

## Page 9: 스택 버퍼 오버플로우 복습 (Recap: Stack Buffer Overflow)
- 버퍼를 초과하여 쓰기를 수행해 **반환 주소(Return address)**를 덮어써 제어 흐름을 조작함.

## Page 10: 스택 카나리 개념 (Stack Canary: Concept)
- 버퍼와 **Saved RBP** 사이에 위치하는 **무작위 값(Random value)**.
- 스레드 로컬 스토리지(**TLS**)에서 생성되며, 함수 반환 전에 값이 변경되었는지 검사하여 오버플로우를 탐지함.

## Page 11: 어셈블리에서의 카나리 (Canary in Assembly)
- 프롤로그(Prologue): TLS(`fs:[0x28]`)에서 카나리를 불러와 스택에 저장.
- 에필로그(Epilogue): 스택의 카나리와 TLS의 값을 비교하며, 불일치 시 `__stack_chk_fail`을 호출해 **프로세스를 즉시 종료**함.

## Page 12: 카나리 컴파일러 옵션 (Compiler Options for Stack Canary)
- **-fstack-protector-strong**: 배열이나 주소가 참조되는 지역 변수를 포함하는 함수까지 보호 (권장 옵션).
- **-fno-stack-protector**: 카나리 비활성화 (연구 및 CTF 목적).

## Page 13: 데이터 실행 방지 (Data Execution Prevention, DEP)
- 스택, 힙 등 데이터 메모리 영역에서의 코드 실행을 방지하는 기법.
- **W^X (Writable XOR Executable)**: 메모리 페이지는 동시에 쓰기와 실행 권한을 가질 수 없음을 의미함.
- 하드웨어 DEP(**NX bit**)와 운영체제 커널의 소프트웨어 DEP 지원이 있음. 쉘코드 주입은 막지만 **ROP**는 막지 못함.

## Page 14-15: DEP 메모리 권한 및 활성화 (Memory Segment Permissions and Enabling)
- `.text` (코드) 영역은 **r-x**(실행 가능), `.data`/스택/힙 영역은 **rw-**(실행 불가)로 설정됨.
- `gcc -z noexecstack` (기본값, DEP 켜짐), `gcc -z execstack` (DEP 꺼짐).
- `readelf` 명령어로 **GNU_STACK** 세그먼트의 RWE(실행 가능) 또는 RW(실행 불가) 권한을 확인.

## Page 16: 주소 공간 배치 무작위화 (ASLR)
- 실행될 때마다 스택, 힙, 공유 라이브러리의 시작 주소를 무작위로 재배치하여 고정된 주소를 이용한 익스플로잇을 방지함.
- 커널 레벨 설정으로 `/proc/sys/kernel/randomize_va_space`에서 관리.

## Page 17-18: ASLR 레벨 및 효과 (ASLR Levels and Effect)
- **Level 0**: 무작위화 없음.
- **Level 1**: 스택, 공유 라이브러리 무작위화.
- **Level 2**: 스택, 공유 라이브러리, **힙(Heap)**까지 모두 무작위화. (현대 리눅스 기본값)
- PIE가 적용되지 않으면 메인 실행 파일(main)의 주소는 고정됨.

## Page 19: 절대 주소 지정 vs RIP 상대 주소 지정 (Absolute vs. RIP-Relative Addressing)
- **No PIE**: 링크 시점에 하드코딩된 **절대 주소(Absolute addresses)** 사용.
- **PIE**: 현재 명령어 위치를 기준으로 하는 **RIP 상대 주소 지정(RIP-relative addressing)** 사용.

## Page 20: PIC와 PIE (PIC and PIE)
- **PIC (Position Independent Code)**: 위치 독립 코드. 공유 라이브러리(`.so`)를 위해 설계됨 (`-fPIC`).
- **PIE (Position Independent Executable)**: 위치 독립 실행 파일. 메인 실행 파일의 베이스 주소를 무작위화함 (`-fPIE -pie`). **ASLR Level 2** 환경에서 완벽히 동작함.

## Page 21-22: 컴파일러 옵션 및 검증 (Compiler Options and Verification)
- `readelf -h` 확인 시 No PIE는 **ET_EXEC**, PIE 적용 시 동적 공유 객체 타입인 **ET_DYN**으로 표시됨.
- ASLR과 PIE가 결합되면 메모리의 모든 영역(main, heap, libc, stack)이 무작위화됨.

## Page 23-24: 방어 계층과 시너지 (Defense Layers & Combined Effect)
- 다양한 보호 기법이 중첩될수록 공격자는 **기법들을 연계(Chain)**해야 하므로 난이도가 기하급수적으로 상승함.
- 예: ASLR + PIE 환경을 뚫으려면 런타임 주소와 바이너리 베이스 주소를 모두 유출(Leak)해야 함.

## Page 25: 보호 기법 우회 개요 (Overview: Bypassing Protection Mechanisms)
- 스택 카나리 $\rightarrow$ 카나리 릭(Leak) 및 브루트 포스.
- DEP $\rightarrow$ **Return-to-Libc (ret2libc)**, **ROP 체인**.
- ASLR/PIE $\rightarrow$ 메모리 유출(Memory disclosure) 취약점을 통한 주소 릭.

## Page 26-27: 스택 카나리 우회 원리 (Bypassing Stack Canary)
- **포맷 스트링 버그(Format String Bug)** 실습 원리: `%p` 포맷 스펙파이어를 악용하여 스택에 있는 카나리 값을 읽어옴(Leak).
- 오버플로우 페이로드 작성 시, 덮어쓰는 과정에서 알아낸 카나리 값을 원본 위치에 그대로 덮어써 무결성 검사를 우회함.

## Page 28: DEP 우회: Return-to-libc (Bypassing DEP: ret2libc)
- 쉘코드를 주입하는 대신 메모리에 이미 실행 권한을 가지고 로드된 libc 함수(`system("/bin/sh")`)의 주소로 제어 흐름을 돌리는 공격.
- x86-64 호출 규약에 따라 RDI 레지스터에 인자("/bin/sh" 주소)를 설정해야 함.

## Page 29: DEP 우회: ROP (Bypassing DEP: ROP)
- 스택에 여러 개의 가젯(Gadget) 주소를 배치하여 `ret` 명령어가 스택에서 다음 실행할 주소를 순차적으로 꺼내어(pop) 실행하게 만드는 공격 원리.

## Page 30: ASLR 및 PIE 우회 원리 (Bypassing ASLR/PIE: Memory Leak)
- 런타임 메모리 유출 실습 원리: GOT 엔트리 등 이미 알려진 오프셋을 가진 런타임 포인터를 읽어냄(Leak).
- **유출된 주소 - 알려진 오프셋 = 라이브러리(또는 바이너리) 베이스 주소** 공식을 통해 전체 메모리 레이아웃을 계산하여 우회함.
