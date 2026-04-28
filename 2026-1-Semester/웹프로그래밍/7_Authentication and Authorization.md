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












