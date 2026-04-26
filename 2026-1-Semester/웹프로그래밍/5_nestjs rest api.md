- Nest.js REST API

  1. REST API와 HTTP 통신
    - 웹 백엔드 서버를 설계할 때는 개발자들이 공통으로 따르는 구조적 규칙이 필요하다
    - 그것이 REST(Representational State Transfer) 아키텍처이다

    - REST의 원칙
      - 상태를 저장하지 않는 무상태성을 가진다
      - 서버는 이전 요청을 기억하지 않으며, 클라이언트와 서버는 오직 데이터 형태(주로 JSON)만 공유하며 독립적으로 통신한다
    - <img width="739" height="226" alt="image" src="https://github.com/user-attachments/assets/ebe4a7d8-49f2-4c9a-bdf5-7929f90916d5" />
      - 클라이언트(PC)가 화살표를 통해 서버로 HTTP Request(요청)을 보내면, 서버는 이를 처리한 뒤 다시 클라이언트로 HTTP Response(응답, JSON 형태)를 돌려보내는 단방향 통신의 반복 사이클을 보여준다
    - 자원(Resource) + HTTP 메서드(Method) 조합
      - 이전에는 /getReports, /createReport처럼 제각각이던 URL을 명사(자원)와 동사(메서드)를 분리해 규격화한다
      - GET(조회), POST(생성, 본문 포함), PATCH(일부 수정), DELETE(삭제)로 동작을 명시한다

    - HTTP 상태 코드(Status Codes): 클라이언트에게 처리 결과를 숫자로 알려준다
      - 2XX(성공): 200(성공), 201(생성됨 - POST 성공 시)
      - 4XX(클라이언트 에러): 400(잘못된 요청 데이터), 401(미인증), 403(권한 없음), 404(찾을 수 없음)
      - 5XX(서버 에러): 500(서버 내부 다운)
     
  2. Controller(라우팅 및 데이터 추출 전담)
    - 역할 분리 아키텍처: Controller는 우편물 분류 센터와 같다
    - 요청을 받아 알맞은 곳으로 넘겨주기만 할 뿐, **절대 내부에서 직접 비즈니스 로직(데이터 처리)을 수행하지 않는다**
    - <img width="882" height="199" alt="image" src="https://github.com/user-attachments/assets/794c341a-1d3e-49ea-9f3a-9924af5c65c9" />
    - HTTP 요청이 먼저 Controller(라우팅 전담)로 들어온다
    - Controller는 화살표를 따라 Service(로직 전담)를 호출하고, 처리가 끝나면 다시 Controller를 거쳐 HTTP 응답으로 나간다
    - 데이터 추출용 데코레이터: 클라이언트가 보낸 데이터를 뽑아낼 때, 복잡한 코드 없이 데코레이터만 붙이면 된다
      - @Param('id'): URL 경로에 있는 변수를 뽑아낸다 // /reports/5에서 5
      - @Query('page'): URL 끝 물음표 뒤의 값을 뽑아낸다 // ?page=2
      - @Body(): POST 요청 등에 담겨 온 JSON 본문 전체를 뽑아낸다
    - 응답 제어 데코레이터
      - Nest.js는 성공 시 기본적으로 200번 코드를 반환하지만, 이를 강제로 바꾸고 싶을 때 @HttpCode(201)를 쓰거나, 응답 헤더에 데이터를 추가할 때 @Header()를 사용한다
  
  3. Provider와 ConfigModule
    - Provider의 종류: Controller를 제외하고 의존성 주입(DI)이 가능한 모든 클래스를 뜻한다
      - Service(비즈니스 로직), Repository(DB 접근), Factory(동적 객체 생성), Helper(공통 유틸리티 기능) 등이 있다
      - ConfigModule: 프로젝트를 깃허브 등에 올릴 때 DB 비밀번호나 JMT 시크릿 키가 유출되지 않도록, 환경 변수(.env 파일)를 안전하게 읽어오는 동적 모듈이다. AppModule에서 isGlobal: true로 한 번만 설정해 두면 앱 전체에서 자유롭게 불러다 쓸 수 있다
  
  4. 요청 파이프라인(Pipe, Middleware, Exception Filter, Interceptor)
    - LM 피셜: 얘네 구성요소의 실행 순서, 각각의 역할 차이 나올 것 같..
    - <img width="902" height="268" alt="image" src="https://github.com/user-attachments/assets/3a12add2-b334-447b-ab15-b34cc02801f4" />
      - HTTP 요청이 핸들러(Controller)에 도달하기까지 위와 같은 순서의 검문소를 거친다
      - 이 과정 중 에러가 발생하면 별도로 튀어나와 Exception Filter가 가로채어 응답을 내보낸다
    
    - Pipe(데이터 변환 및 검증)
      - Controller 도달 직전에 실행되어, 입력 데이터의 타입 변환과 유효성 검증을 전담한다
      - class-validator 데코레이터(@IsString(), @IsInt())를 이용해 규칙을 정하고, 평범한 JSON 데이터를 실제 DTO 클래스 객체로 바꿔주는 class-transformer와 결합하여 전역 ValidationPipe로 방어막은 친다
    
    - Middleware(요청/응답 기초 가공)
      - 가장 먼저 실행되는 계층으로 req, res, next()에 접근한다
      - 다음 단계로 넘어가려면 반드시 next()를 호출해야한다
      - 주로 모든  요청의 URL과 응답 시간을 기록하는 Logging(로깅)에 사용된다
 
    - Exception Filter(예외 처리 규격화)
      - 앱 어디서든 처리되지 않은 에러(Exception)가 터지면 이를 가로채서, 클라이언트가 파싱하기 쉽도록 규격화된 JSON 형태로 에러 응답을 포맷팅한다
      - Nest.js는 NotFoundException(404), BadRequestException(400) 같은 내장 예외 클래스를 기본 제공한다
     
    - Interceptor(핸들러 실행 전후 제어)
      - RxJS 라이브러리(Observable 패턴)를 기반으로 작동하며, 주로 핸들러가 반환한 데이터를 클라이언트에게 보내기 직전에 일괄적으로 형태를 변형(Response transformation)할 때 사용한다
     
    - **정리**
      - 미들웨어: 로깅, 기초 데이터 파싱(RxJS 불필요, req/res 원시 데이터 접근)
      - 인터셉터: 반환 데이터 구조 일괄 변형(RxJS 사용)
      - 예외 필터: 오직 에러가 터졌을 때만 동작하여 에러 메시지 포맷
