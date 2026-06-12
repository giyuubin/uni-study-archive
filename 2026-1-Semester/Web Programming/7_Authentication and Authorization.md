- Authentication(인증) and Authorization(인가)
  - 이제는 내부자도 믿을 수 없다!
  - 인증:
    - "당신 누구야?"의 답변
    - Commen methods:
      - ID/password, OTP, biometrics, OAuth // 우리가 제공하는 정보
    - <img width="805" height="202" alt="image" src="https://github.com/user-attachments/assets/8f5ed68d-a5bf-4e19-be76-798a66df216e" />

  - 인가: 역할 => 자료
  - <img width="870" height="193" alt="image" src="https://github.com/user-attachments/assets/2d2c3817-c620-48fe-8b59-b4cf947a54b8" />

  - ID/PW-based Authentication
    - 원칙 // 각각 구분이 중요
      1. Identification
      2. Authentication
    - <img width="822" height="233" alt="image" src="https://github.com/user-attachments/assets/249cda9b-a01a-4c55-b050-5661ef8b6da9" />

    - Google service가 정석적으로 구현함. // ID/PW 따로따로 해야함
    - <img width="878" height="197" alt="image" src="https://github.com/user-attachments/assets/d0d822e1-f528-42fb-ac57-ab83b806bd01" />

  - bcrypt(Ex: 해시 함수)
    - Hash Function:
      - one-way // 역산 불가
      - Input => Output
      - 고정된 길이의 출력
      - <img width="875" height="118" alt="image" src="https://github.com/user-attachments/assets/72e01181-147f-43ec-a1cc-916e0bb63811" />
        - SHA-256: 256bit
      - Salt, Cost factor(rounds) =| rainbow table: 해시 함수 미리 계산 => 일치하는지 비교 => 뚫림
      - <img width="879" height="161" alt="image" src="https://github.com/user-attachments/assets/3dc79567-5ec5-47a7-89e2-20c28f6ef66f" />
        - bcrypt: 병렬 계산 개빡셈 => 개느림, Salt 들어가 있음
      - <img width="431" height="160" alt="image" src="https://github.com/user-attachments/assets/7e93c08f-e12e-497c-9955-950b877407dd" />
        - $2b$: bcrypt 식별자
        - 10$: cost, $2^1{0}$
        - ws~: salt
        - mH~: hash
      - Hash 여러번 반복($2^{10}$번)

---
# 7단원: Authentication and Authorization
## Page 1: 웹 프로그래밍 7단원 도입
- [cite_start]가천대학교 스마트보안 전공 웹 프로그래밍 7단원: **Authentication and Authorization(인증과 인가)**[cite: 1, 2, 3, 4].

## Page 2: What is Authentication? (인증이란?)
- [cite_start]**Authentication(인증)**은 "당신은 누구인가?(Who are you?)"라는 질문에 대한 답을 구하는 과정이다[cite: 7, 8].
- [cite_start]사용자가 주장하는 **Identity(신원)**를 검증하는 과정이다[cite: 9].
- [cite_start]공통적인 방법으로 **ID/password**, OTP, **biometrics(생체 인식)**, OAuth 등이 있다[cite: 10, 11].
- [cite_start]성공적인 인증의 결과물로 이후 요청에 사용될 **Token(토큰)**이 발급된다[cite: 13].

## Page 3: What is Authorization? (인가란?)
- [cite_start]**Authorization(인가)**은 "무엇을 하도록 허락받았는가?(What are you allowed to do?)"에 대한 답이다[cite: 21, 22].
- [cite_start]신원이 먼저 파악되어야 하므로, 반드시 **Authentication(인증) 이후**에 발생한다[cite: 23].
- [cite_start]사용자가 접근할 수 있는 **Resource(리소스)**와 허용된 **Action(동작)**을 정의한다[cite: 24].

## Page 4: Authentication vs. Authorization
- [cite_start]인증과 인가는 종종 혼동되지만 명확히 구분되는 단계이며, **인증이 먼저, 인가가 그 다음**에 온다[cite: 26, 27].
- [cite_start]**인증(Authentication)** 실패 시 HTTP 상태 코드 **401 Unauthorized**가 발생하며, 입력은 인증 정보(ID+PW), 출력은 **Token(JWT)**이다[cite: 28].
- [cite_start]**인가(Authorization)** 실패 시 HTTP 상태 코드 **403 Forbidden**이 발생하며, 입력은 검증된 신원과 역할(Role), 출력은 접근 허용 또는 거부이다[cite: 28].

## Page 5: ID/PW-based Authentication의 2단계 프로세스
- [cite_start]로그인(Login)은 단일 작업이 아니라 **Identification(식별)**과 **Authentication(인증)**의 두 단계 프로세스로 진행된다[cite: 29, 30].
- [cite_start]**Step 1. Identification**: 사용자가 ID(이메일)를 제공하여 서버에 자신이 누구인지 알린다[cite: 31].
- [cite_start]**Step 2. Authentication**: 사용자가 PW(비밀번호)를 제공하여 자신이 해당 인물임을 증명한다[cite: 32].
- [cite_start]서버는 사용자를 식별(Identify)하기 전에는 인증(Authenticate)할 수 없다[cite: 33].

## Page 6: ID (Identification)
- [cite_start]**ID(이메일 또는 사용자명)**는 비밀이 아닌 공개된 **Identifier(식별자)**이다[cite: 45, 46].
- [cite_start]식별의 유일한 목적은 데이터베이스에서 올바른 사용자 레코드를 찾는 것이다[cite: 47].
- [cite_start]식별만으로는 어떠한 권한도 부여되지 않으며, 단지 인증할 사용자를 선택할 뿐이다[cite: 48].

## Page 7: Password (Authentication)
- [cite_start]**Password(비밀번호)**는 사용자와 서버만 알고 있는 공유된 **Secret(비밀)**이다[cite: 60, 61].
- [cite_start]해당 ID를 입력한 사람이 실제 그 계정의 소유자임을 증명하는 목적으로 쓰인다[cite: 62].
- [cite_start]서버는 비밀번호를 **Plain text(평문)**로 절대 저장하지 않으며 오직 **Hash(해시)**만을 저장한다[cite: 63].
- [cite_start]올바른 비밀번호로 신원이 확인되면 서버는 **Token(토큰)**을 발급한다[cite: 64].

## Page 8: Registration Flow (회원가입 흐름)
- [cite_start]회원가입 진행 시, 비밀번호는 DB에 저장되기 전에 반드시 **Hashed(해싱)** 되어야 한다[cite: 81, 82].
- [cite_start]평문 비밀번호(Plain text password)는 어떠한 곳에도 영구적으로 저장(Persisted)되어서는 안 된다[cite: 83].

## Page 9: Login Flow (로그인 흐름)
- [cite_start]로그인은 **Identification(식별)**과 **Authentication(인증)** 두 개의 구별된 검사로 나뉜다[cite: 90, 91].
- [cite_start]`bcrypt`의 `compare()` 함수가 비밀번호 검증을 처리하며 평문은 저장되지 않는다[cite: 92].
- [cite_start]서버가 이메일을 찾지 못하거나 비밀번호 매칭에 실패하면 **401 에러**를 반환하고, 성공하면 비밀키로 서명된 **JWT (access_token)**를 생성해 클라이언트에 반환한다[cite: 94, 95, 96, 97].

## Page 10: What is a Hash Function? (해시 함수란?)
- [cite_start]**Hash Function(해시 함수)**는 임의의 입력을 받아 **Hash(해시)**라 불리는 고정된 길이의 출력을 생성한다[cite: 99, 100].
- [cite_start]**One-way(단방향)**: 해시값으로부터 원본 입력을 복구할 수 없다[cite: 101].
- [cite_start]**Deterministic(결정론적)**: 동일한 입력은 항상 동일한 출력을 생성한다[cite: 102, 103].
- [cite_start]입력값에 아주 미세한 변화만 생겨도 완전히 다른 해시가 만들어진다[cite: 104].

## Page 11: Why Not SHA-256 or MD5?
- [cite_start]SHA-256과 MD5는 범용 해시 함수로 연산 속도가 매우 빠르도록 설계되었다[cite: 107, 108, 109].
- [cite_start]빠른 해싱은 공격자가 초당 수십억 번의 추측을 가능하게 하므로 비밀번호 저장에 매우 위험하다[cite: 110].
- [cite_start]공격자는 흔한 비밀번호의 해시를 미리 계산해둔 **Rainbow table(레인보우 테이블)** 공격을 사용할 수 있다[cite: 111, 112].
- [cite_start]따라서 비밀번호 해싱에는 의도적으로 연산이 느리고 사용자마다 고유한 함수가 필요하다[cite: 113].

## Page 12-13: bcrypt 알고리즘의 특징
- [cite_start]**bcrypt**는 기본 해싱 알고리즘 위에 **Salt(솔트)**와 **Cost factor(비용 인자)**라는 두 가지 보호 기법을 추가한다[cite: 116, 117].
- [cite_start]**Salt**: 해싱 전 비밀번호에 추가되는 무작위 문자열로, 모든 사용자가 고유한 솔트를 가지므로 동일한 비밀번호라도 완전히 다른 해시 결과가 나온다 (해시와 함께 저장됨)[cite: 118, 119, 120].
- [cite_start]**Cost factor (Rounds)**: 해싱 연산의 반복 횟수를 제어하며, 값이 클수록 연산이 느려져 **Brute-force(무차별 대입 공격)**를 방어한다 (권장값: 10)[cite: 121, 122, 123].
- [cite_start]bcrypt 해시 문자열 구조: `Algorithm + Cost factor + Random Salt + Hash`[cite: 134].

## Page 14-15: bcrypt를 활용한 패스워드 검증 원리 (Lab 핵심)
- [cite_start]회원가입 시: `bcrypt.hash()` 메서드에 평문과 라운드(Rounds) 값을 넣어 **해시를 생성 및 저장**한다[cite: 144, 151].
- [cite_start]로그인 시: `bcrypt.compare()` 메서드를 통해 저장된 해시에 내장된 솔트(Salt)를 사용해 입력된 평문을 재해싱한 후, 두 값을 비교하여 일치 여부를 **boolean(true/false)**으로 반환한다[cite: 154, 155].
- [cite_start]검증 과정 중 어떤 시점에도 평문 비밀번호가 저장되는 일은 없다[cite: 156].

## Page 16: JWT (JSON Web Token) 개요
- [cite_start]**JWT**는 당사자 간에 정보를 안전하게 전송하기 위한 작고 독립적인 개방형 표준(Open standard)이다[cite: 164, 165].
- [cite_start]로그인 성공 시 서버가 발급하며, 클라이언트는 이후 모든 후속 요청에 JWT를 첨부하여 전송한다[cite: 167, 168].
- [cite_start]서버는 요청마다 데이터베이스를 조회하는 대신 토큰의 **Signature(서명)**를 확인하는 방식으로 검증한다[cite: 169].

## Page 17: JWT Structure (JWT 구조)
- [cite_start]JWT는 점(.)으로 구분된 3가지 파트(**header . payload . signature**)로 구성된다[cite: 177, 178, 179].
- [cite_start]각 파트는 암호화(Encrypted)된 것이 아닌 **Base64Url**로 인코딩된 것이므로, **Payload(페이로드)는 누구나 읽을 수 있다**[cite: 180].
- [cite_start]**Signature(서명)**는 토큰이 중간에 변조(Tampered)되지 않았음을 수학적으로 보장한다[cite: 181].
- [cite_start]Header에는 알고리즘과 토큰 타입, Payload에는 사용자 데이터(Claims), Signature에는 Header와 Payload를 조합한 HMAC 해시가 담긴다[cite: 182].

## Page 18: JWT Payload, Claims
- [cite_start]JWT의 **Payload**는 토큰 주체에 대한 정보를 담은 키-값 쌍인 **Claims(클레임)**로 구성된다[cite: 184, 185].
- [cite_start]데이터가 누구나 읽을 수 있게 노출되므로 **절대 민감한 정보(비밀번호 등)를 페이로드에 저장해서는 안 된다**[cite: 186].
- [cite_start]공통 Claims: **sub** (주체/사용자 ID), **email** (사용자 이메일), **role** (역할), **iat** (발급 시간), **exp** (만료 시간)[cite: 187, 188, 189, 191, 192].

## Page 19-21: Token Signing & Verifying (토큰 서명과 검증 로직)
- [cite_start]**Token Signing(발급)**: Base64로 인코딩된 Header와 Payload를 합친 후, 서버만이 아는 **SERVER_SECRET(비밀키)**를 더해 **HMAC-SHA256** 알고리즘으로 **Signature(서명)**를 생성한다[cite: 195, 201, 203, 205, 206].
- [cite_start]**Token Verifying(검증)**: 서버는 발급한 토큰을 DB에 저장하지 않는 **Stateless(무상태)** 검증 방식을 사용한다[cite: 210, 211].
- [cite_start]수신한 토큰의 Header와 Payload를 비밀키와 함께 다시 연산하여 서명을 재계산한 후, 토큰의 기존 서명과 비교(Comparing)한다[cite: 212, 245, 246].
- [cite_start]페이로드가 수정되었거나, 토큰이 만료(`exp` 시간이 지남)된 경우 해당 JWT는 거부(Rejected)된다[cite: 213, 214].

## Page 22-24: Nest.js에서의 JWT 적용 원리 (Lab 핵심)
- [cite_start]**Module Setup**: `JwtModule.register()`를 사용해 JWT 서명을 위한 전역 Secret Key와 토큰 만료(Expiration) 시간을 설정한다[cite: 248, 249].
- [cite_start]**Signing**: 로그인 완료 시 사용자 정보 전체가 아닌 인가(Authorization)에 필요한 최소한의 데이터만 페이로드로 담아 `JwtService.sign()`을 호출해 `access_token`을 생성한다[cite: 263, 264, 266].
- [cite_start]**Verifying**: 토큰 검증 로직은 컨트롤러가 아닌 **JwtAuthGuard**가 전담하며, HTTP 헤더의 `Authorization: Bearer <token>`에서 토큰을 추출해 검증하고 해독된 페이로드를 `request.user` 객체에 첨부한다[cite: 274, 275, 276, 277].

## Page 25-28: Nest.js Auth 구현 및 Guard (Lab 핵심)
- [cite_start]회원가입(`register`) 시 이메일 중복 가입을 사전에 차단하고, DB에 기록하기 전에 비밀번호를 해싱하며 평문은 즉시 파기한다[cite: 308, 309, 310].
- [cite_start]로그인(`login`) 시 식별(Identification) 후 인증(Authentication) 과정을 거치며, 실패 시 사용자 열거(Enumeration) 공격 방지를 위해 모호한 **401 Unauthorized** 에러를 일괄 반환한다[cite: 321, 322].
- [cite_start]컨트롤러나 라우터 보호 시 `@UseGuards(JwtAuthGuard)` 데코레이터를 적용하여 검증된 사용자만 접근할 수 있게 제어한다[cite: 339].

## Page 29: Role-Based Access Control (역할 기반 접근 제어)
- [cite_start]**RBAC**: 인증을 통과한 후, 사용자의 **Role(역할, 예: USER, ADMIN)**에 따라 특정 라우트에 대한 접근 및 동작 권한을 통제하는 방식이다[cite: 351, 352, 353].
- [cite_start]Nest.js에서는 `@Roles()` 커스텀 데코레이터와 **RolesGuard**를 결합하여, 라우트에 설정된 권한 메타데이터와 요청자의 `req.user.role`을 비교 검사한다[cite: 354, 355].

## Page 30: @CurrentUser() Custom Decorator (Lab 핵심)
- [cite_start]`JwtAuthGuard`를 통과하여 `request.user`에 저장된 JWT 페이로드 정보를 컨트롤러 단에서 더 직관적이고 깔끔하게 추출하기 위해 생성하는 커스텀 파라미터 데코레이터이다[cite: 367, 368, 369].
- [cite_start]이를 통해 매번 직접 `@Request()` 객체를 주입받아 분해하는 반복적인 코드를 줄일 수 있다[cite: 370].
