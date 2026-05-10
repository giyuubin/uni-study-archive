- Database

  1. In-Memory 저장의 한계와 데이터베이스의 필요성
     - 5장에서 private reports: Report[] = [] 형태의 배열을 서버 메모리에 두고 데이터를 저장했다
     - 한계점: 서버를 재시작하면 모든 데이터가 날아가며, 서버가 여러 대일 경우 서로 데이터를 공유할 수 없고 확장이 불가능하다
     - DB의 도입: DB는 서버가 꺼져도 데이터를 영구적으로 보존(Persistence)하며, 여러 사용자가 동시에 접근(Concurrency)해도 안전하게 처리하고, 규칙(Integrity)을 강제하여 무결성을 유지한다
       // 우리는 MySQL 사용함

  2. 테이블 관계와 ER Diagram
     - 관계형 데이터베이스는 데이터를 행과 열로 이루어진 Table 형태로 저장한다
     - 데이터 중복을 막기 위해 테이블을 쪼개고 이를 연결하는 것이 핵심이다
     - 1:N(One-to-Many): 가장 흔한 구조. 한 명의 유저(1)가 여러 개의 리포트(N)를 작성하는 구조. 이 때 연결 고리가 되는 외래 키는 항상 다(N)쪽인 리포트 테이블에 위치한다
       - <img width="646" height="115" alt="image" src="https://github.com/user-attachments/assets/5efdc64f-7eb5-4218-af0d-25495041e117" />
     - N:M(Many-to-Many): 하나의 리포트가 여러 태그를 가질 수 있고, 하나의 태그도 여러 리포트에 쓰일 수 있다. 이 경우 단순한 외래 키로는 연결할 수 없어, 두 테이블 사이에 다리 역할을 하는 중간 연결 테이블이 필요하다
       - <img width="787" height="136" alt="image" src="https://github.com/user-attachments/assets/ae4ceda5-b872-4017-9fa6-590211d41b3c" />
     - ER Diagram(ERD): 코드를 짜기 전 데이터베이스 구조를 시각적으로 기획하는 설계도
       - <img width="583" height="408" alt="image" src="https://github.com/user-attachments/assets/ae5a3ddb-53c7-4aac-bd5e-a0706d040f73" />
         - 각 테이블은 네모난 상자로 표현되고, 상자들은 실선으로 연결되어 있다
         - 선이 연결되는 끝부분을 보면, 1쪽은 작대기 하나(또는 두 개)로 끝나지만, 다(N)쪽은 세 갈래로 갈라지는 기호를 사용해 1:N 관계를 나타낸다

  3. Docker
     - MySQL을 내 컴퓨터에 직접 설치하면 다른 프로젝트와 버전이 충돌하거나, 운영체제에 따라 세팅이 달라지는 문제가 발생한다
     - Docker: DB와 실행 환경을 통째로 패키징하여 격리된 컨테이너 안에서 실행하게 해주는 도구이다
       - 이를 통해 어떤 컴퓨터에서든 충돌 없이, 명령어 한 줄로 DB를 띄우고 삭제할 수 있게 된다
      
  4. ORM(Object-Relational Mapping)
     - TS 코드 내에서 SELECT * FROM user 같은 순수 SQL 문자열을 직접 쓰면, 오타가 나도 실행 전엔 알 수 없고 SQL 인젝션 공격에 취약해진다
     - <img width="719" height="163" alt="image" src="https://github.com/user-attachments/assets/74520418-6214-40e9-b67c-97cb13e6ebda" />
       - 중간에 있는 ORM 상자가 번역기 역할을 수행한다
       - TS 클래스 -> ORM이 DB 테이블로 변환
       - TS 객체 -> ORM이 DB의 한 행으로 변환
       - TS 메서드 -> ORM이 SQL 쿼리로 자동 변환하여 실행
     - 즉, 개발자는 SQL을 몰라도 안전한 TS 객체지향 문법만으로 DB를 완벽히 제어할 수 있다
       // 우리는 TypeORM 사용함

  5. Nest.js 요청 처리 흐름
     - <img width="493" height="478" alt="image" src="https://github.com/user-attachments/assets/45132c40-0ade-436a-9e6e-a7246b80ce78" />
       - Controller: 클라이언트의 HTTP 요청을 받는다
       - Service: 비즈니스 로직을 수행하다가 데이터가 필요해지면 Repository의 메서드를 호출한다
       - Repository: TypeORM을 이용해 데이터 접근만을 전담한다. SQL을 자동 생성한다
       - Database: SQL을 실행하고 결과를 반환한다
       - 결과가 다시 위로 거슬러 올라가 Controller를 통해 HTTP 응답으로 나간다

  6. TypeORM 설정 구조
     - Nest.js와 TypeORM을 연결하기 위해 두 가지 설정 뼈대가 필요함
     - 전역 설정(AppModule): TypeOrmModule.forRootAsync를 사용한다. 이때 ConfigModule을 함께 가져와 환경변수(.env)에 숨겨둔 DB 비번과 포트 번호를 안전하게 읽어온다
       - synchronize: true: 이 옵션을 켜두면, 작성한 TS 클래스 구조를 읽고 서버가 켜질 때마다 알아서 MySQL에 테이블을 생성하거나 수정해준다
     - 모듈별 설정: 각 기능 모듈(Ex: ReportsModule)안에 TypeOrmModule.forFeature([Report])를 등록하여 해당 테이블을 다루겠다는 것을 명시한다

  7. Entity
     - DB 테이블의 설계도가 되는 클래스다
     - @Entity('테이블명'): 이 클래스가 DB 테이블임을 선언한다
     - @PrimaryGeneratedColumn(): 데이터가 추가될 때마다 1, 2, 3...순서대로 겹치지 않게 자동 부여되는 기본 키(PK)를 생성한다
     - @Column(): 일반 속성(제목, 내용 등)을 테이블의 열로 만든다
    
  8. 관계형 데코레이터(@ManyToOne, @OneToMany)
     - LM 피셜 중요타함
     - @ManyToOne(다 대 일): 항상 외래 키(FK)를 실제로 소유하는 다(N) 측에 작성한다
       - 이 밑에는 반드시 @JoinColumn({ name: 'userID' })를 달아 실제 생성될 외래 키 컬럼의 이름을 명시한다
     - @OneToMany(일 대 다): 반대편인 1측에 작성한다
       - 여기에는 @JoinColumn을 달지 않으며, 실제 테이블에 컬럼도 생기지 않는 가상의 연결 고리 역할만 한다

  9. Repository
     - Service 계층의 생성자에서 @InjectRepository(엔티티)를 통해 저장소를 주입(DI)받아 사용한다
       - create와 save: 새로운 데이터를 넣을 때 항상 세트로 쓰인다
         - create: DB는 건드리지 않고 메모리에 객체만 찍어낸다
         - save: save를 호출해야만 실제 MySQL에 INSERT된다
       - find와 findOne: 배열 전체를 찾거나, 특정 조건(id 등)에 맞는 단일 데이터를 조회한다
       - remove: 데이터를 지울 때 사용한다
         - 단 무작정 지우지 않고 findOne으로 먼저 데이터가 존재하는지 로드하여 권한 등을 확인한 후 remove로 넘기는 패턴이 정석이다
