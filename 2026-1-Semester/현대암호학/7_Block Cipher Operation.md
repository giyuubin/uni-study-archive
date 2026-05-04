- Multiple Encryption and Triple DES
  - DES의 취약점 해결 방안:
    - DES에 대한 Brute force attack(무차별 대입 공격)의 위협이 커지면서 이를 해결하기 위해 AES 같은 알고리즘을 설계하거나, 기존 DES에 여러 키를 사용하여 여러 번 암호화하는 다중 암호화 방식이 등장했다
    - 이는 기존의 소프트웨어 및 하드웨어 장비 모듈을 그대로 재활용할 수 있다는 장점이 있다 // 개 아까움

  - Sol1) Double DES
    - <img width="465" height="311" alt="image" src="https://github.com/user-attachments/assets/8b024685-084b-48b2-9012-58c829e8a9e2" />

    - 암호화 과정 $C = E(K_2, E(K_1, P))$와 복호화 과정 $P = D(K_1, D(K_2, C))$로 두 번 DES를 거치는 구조

  - P1) Meet-in-the-Middle Attack(중간자 공격)
    - Double DES 방식을 무력화하는 공격 기법
    - 알려진 평문-암호화 쌍 $(P, C)$에 대해, 키 $K_1$의 모든 가능한 조합($2^{56}$개)으로 평문 $P$를 암호화하여 중간값 $X$를 계산한다
    - 동시에 키 $K_2$의 모든 가능한 조합($2^{56}$개)으로 암호문 $C$를 복호화하여 중간값 $X$를 계산한다
    - 일치하는 쌍이 나오면 이를 이용해 새로운 $(P', C')$쌍에 대해 암호화를 시도하며 확인한다
   
  - Sol2) Triple DES with Two/Three Keys // DES를 Two/Three 진행, Key 수X
    - <img width="540" height="333" alt="image" src="https://github.com/user-attachments/assets/3cae5831-8e72-482f-ad0e-97dad43e323a" />

    - 2개의 키를 사용하는 3DES
    - 암호화-복호화-암호화(EDE) 순서로 $C = E(K_1, D(K_2, E(K_1, P)))$ 연산을 수행함
---
- Mode of Operation(운영 모드)
  - 광범위한 애플리케이션을 포괄하기 위해 5가지의 운용모드 ECG, CBC, CFB, OFB, CTR
  - 이 모드들은 Triple DES 및 AES를 비롯한 모든 대칭 블록 암호에 적용 가능하다
  - ECB(Electronic Codebook)
    - <img width="404" height="385" alt="image" src="https://github.com/user-attachments/assets/e74e9432-806e-4c83-9064-4e94600ce12e" />

    - 평문의 각 블록을 독립적으로 동일한 키를 사용하여 인코딩하는 가장 단순한 방식
    - $C_1 = E(K, P_1), C_2 = E(K, P_2)$...와 같이 전개됨
    - **ECB의 취약점**: // 잘 안 쓰이는 이유
      - 메시지가 고도로 구조화되어 있거나 반복적인 패턴이 있다면 동일한 평문 블록이 동일한 암호문 블록을 생성하게 되어 패턴이 노출된다
      - <img width="617" height="267" alt="image" src="https://github.com/user-attachments/assets/5f71392e-e38e-4544-9d56-419d628123df" />
---
- CBC(Cipher Block **Chaining** Mode)
  - 암호화 알고리즘의 입력으로 다음 평문 블록과 이전 암호문 블록을 배타적 논리합(XOR)한 값을 사용함
  - 수식:
    - 암호화: $C_j = E(K, [C_{j-1} \oplus p_j])$
    - 복호화: $D(K, C_j) = D(K, E(K, [C_{j-1} \oplus P_j])) = C_{j-1} \oplus P_j$
    - 최종 평문 산출:$C_{j-1} \oplus D(K, C_j) = C_{j-1} \oplus C_{j-1} \oplus P_j = P_j$
  - Initialzation Vector(IV): 첫 번째 평문 블록($P_1$)을 위해 사용되는 IV는 송신자와 수신자 모두 알고 있어야 하며, 제3자가 예측 불가능해야 한다 // 아 그러쿠나..
  - 장점: 평문의 패턴이 반복되더라도 Chaining 과정 때문에 서로 전혀 다른 암호문 블록이 생성되는 확산 효과 제공
  - **단점**:
    - 암호화 과정에서 피드백 구조 때문에 각 블록을 병렬로 암호화할 수 없다
    - 구조상 Error 발생시 계속 전파됨
---






