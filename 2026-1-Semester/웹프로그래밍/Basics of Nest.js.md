- Basics of Nest.js
  
  1. 디자인 패턴과 MVC 아키텍처
    - 개발자들이 만들어둔 재사용 가능한 설계 템플릿을 디자인 패턴이라 한다
    - 코드를 일관성 있게 만들고 유지보수를 돕는다
    - 이중 가장 널리 쓰이는 건축 패턴이 MVC 패턴이다
      
    - <img width="443" height="274" alt="image" src="https://github.com/user-attachments/assets/76dc7c93-c96b-446d-9af2-7527cda73f0d" />
      - 사용자가 입력을 보내면 먼저 Controller가 요청을 받는다
      - Controller는 Model에게 데이터 처리나 업데이트를 지시한다
      - Model은 데이터베이스와 통신하여 비즈니스 로직을 처리한 뒤, View에게 데이터 변경되었음을 알린다
      - View는 최종적으로 변경된 데이터를 화면에 그려 사용자에게 보여준다
    
    - 각 요소의 역할
      - Model: 데이터의 구조와 비즈니스 규칙을 전담
      - View: 컨트롤러가 전달한 데이터를 사용자에게 시각적으로 보여주는 UI 전담
      - Controller: 사용자의 요청(HTTP)을 받아 Model과 View 사이를 조율하는 전담
    
    - 발전형 - MVVM(Model-View-ViewModel)
      - 최근 React나 Nest.js같은 프론트엔드에서는 Controller 대신 데이터 바인딩을 통해
        View가 자동으로 상태 변화를 감지하는 MVVM 패턴을 사용한다

  2. HTTP: Hypertext Transfer Protocol
    - <img width="570" height="248" alt="image" src="https://github.com/user-attachments/assets/81525f21-661d-4d62-a2b0-0298cf59bd1a" />
    - 클라이언트(웹 브라우저)와 서버(Nest.js)가 통신하기 위한 규칙
    - HTTP는 무상태성, 즉 서버가 **이전 요청을 전혀 기억하지 않는다**는 특징을 가진다
    - 클라이언트(PC)가 서버를 향해 Request 화살표를 보내고, 서버가 처리 후 클라이언트 측으로 Response 화살표를 돌려보내는 단방향 통신의 반복 구조
    
    - Request의 구조
      - Method: 무엇을 할지 결정
        - 주요 Method
          - GET: 데이터 조회
          - POST: 데이터 생성
          - PATCH/PUT: 데이터 수정
          - DELETE: 데이터 삭제
      - URL: 목적지
      - Headers: 부가 정보
      - Body: 실제 데이터가 담김
    
    - Response의 구조
      - Status Code, Headers: 결과 상태를 나타냄
        - 주요 Status Code
          - 200번대(성공)
          - 300번대(리다이렉션)
          - 400번대(클라이언트 측 입력/권한 에러)
          - 500번대(서버 내부 다운 에러)
      - Body: 반환 데이터
    
    - **HTTPS**: HTTP에 **TLS/SSL** 암호화 계층을 추가하여 데이터를 안전하게 보호하는 보안 통신 규약
 
  3. Nest.js
    - Node.js의 기본 도구인 Express는 자유도가 높아 개발자마다 코드를 짜는 방식이 달라지는 문제가 있다
    - **Nest.js는 강력한 구조로 규칙을 강제**하여 거대한 프로젝트를 안정적으로 유지하게 해준다
    
    - Nest.js 서버 뼈대 // 한 세트로 묶여 동작함
      - Module(@Module): 관련된 코드들을 하나로 묶어주는 폴더이자 조립 단위.
        모든 Nest.js앱은 최상위 AppModule을 가지며, 그 아래에 UsersModule, ReportModule같은 기능별 모듈 부품처럼 끼워 넣는다
      - Controller(@Controller): 클라이언트의 HTTP 요청(GET, POST 등)을 가장 먼저 받아내고 응답을 반환하는 역할. 내부에 직접 비지니스 로직을 짜는 건 금지
      - Service / Provider(@Injectable): 실제 데이터베이스를 조회하거나 연산을 수행하는 비지니스 로직 전담반이다
      - 요청 처리 흐름(Macro 수준)
        - 클라이언트가 GET / users 요청을 보냄
        - Nest.js 라우터가 해당 URL을 담당하는 Controller 메서드 연결
        - Controller는 Service의 메서드 호출
        - Service가 로직을 처리해 결과 반환
        - Controller가 그 결과를 클라이언트에게 HTTP 응답으로 반환
  
  4. 의존성 주입(DI)과 데코레이터(Decorators)
    - DI(Dependency Injection): Controller가 Service를 사용할 때, 코드 안에서 직접 new Service()로 생성하지 않는다
    - 대신 이 Service가 필요해라고 생성자에 선언만 해두면, Nest.js 시스템이 알아서 Service 객체를 생성해 전달한다
    - 이래하면 컴포넌트간의 결합도가 낮아져 코드를 수정하거나 테스트하기가 쉬워진다

    - 데코레이터(Decorators, @): 클래스나 메서드 위에 붙어 시스템에 부가 정보를 알려주는 표식
      - 클래스 레벨: @Module, @Controller('reports'), @Injectable()
      - 메서드 레벨: @Get(':id'), @Post()처럼 HTTP 메서드와 경로를 매핑함
      - 파라미터 레벨(데이터 추출): 클라이언트가 보낸 데이터를 뽑아냄
        - @Param('id'): URL 경로에서 뽑는다
        - @Query('page'): 쿼리 스트링에서 뽑는다
        - @Body(): JSON 본문 전체를 뽑는다












