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
    - 최종 평문 산출:
      - $C_{j-1} \oplus D(K, C_j)$ = $C_{j-1} \oplus C_{j-1} \oplus P_j$ = $P_j$
  - Initialzation Vector(IV): 첫 번째 평문 블록($P_1$)을 위해 사용되는 IV는 송신자와 수신자 모두 알고 있어야 하며, 제3자가 예측 불가능해야 한다 // 아 그러쿠나..
  - 장점: 평문의 패턴이 반복되더라도 Chaining 과정 때문에 서로 전혀 다른 암호문 블록이 생성되는 확산 효과 제공
  - **단점**:
    - 암호화 과정에서 피드백 구조 때문에 각 블록을 병렬로 암호화할 수 없다
    - 구조상 Error 발생시 계속 전파됨
---
- CFB(Cipher **Feedback** Mode)
  - 블록 암호는 원래 정해진 길이의 블록 단위로 수행되지만, CFB, OFB, CTR 모드를 사용하면 블록 암호를 Stream Cipher처럼 변환하여 사용할 수 있다
  - <img width="638" height="345" alt="image" src="https://github.com/user-attachments/assets/b2abddc8-8279-4729-969d-67a79e4b6d99" />
  - <img width="624" height="348" alt="image" src="https://github.com/user-attachments/assets/97c1b187-2522-4492-b942-c4b56be32d7e" />

  - s-bit CFB 모드:
    - 입력은 한 번에 $s$비트씩 처리된다
    - 이전 암호문은 암호화 알고리즘의 입력으로 사용되어 의사 난수 출력을 생성함
    - 이 출력값의 가장 높은(MSB) $s$비트를 현재 평문과 XOR하여 새로운 암호문 단위를 생성함
  - 수식:
    - 암호화:
      - $I_1 = IV$, $I_j = LSB_{b-s}(I_{j-1})||C_{j-1}$
      - $O_j = E(K, I_j)$
      - $C_j = P_j \oplus MSB_8(O_j)$
    - 복호화: $P_j = C_j \oplus MSB_8(O_j)$
    - 특징: 암호화와 복호화 과정 모두에서 **오직 블록 암호의 암호화(Encryption) 함수만을 사용함**
    - 단점: 암호문 자체를 시프트 레지스터로 피드백시키기 때문에, 전송 중 1비트 에러가 발생하면 후속 블록의 복호화 과정까지 영향을 미치는 오류전파가 존재
---
- OFB(Output Feedback Mode)
  - CFB와 유사하지만, 암호화 알고리즘의 입력으로 사용되는 값이 이전 암호화의 직접적인 출력값($O_{j-1}$)이며, 전체 $b$ 비트 블록 단위로 운용된다는 차이가 있다
  - <img width="629" height="305" alt="image" src="https://github.com/user-attachments/assets/a7231ae4-3d78-4212-9cdd-13d733c73afb" />
  - <img width="615" height="303" alt="image" src="https://github.com/user-attachments/assets/472afe19-1b31-4204-980d-7cb3eda0bd5c" />

  - 수식:
    - 암호화:
      - $I_1 = Nonce$
      - $I_j = O_{j-1}$
      - $O_j = E(K, I_j)$
      - $C_j = P_j \oplus O_j$
    - 마지막 $N$번째 블록(크기가 $u$비트일 때):
      - $C_N^* = P_N^* \oplus MSB_u(O_N)$
    - 장점:
      - 독립적인 키 스트림: 평문이나 암호문의 내용과 전혀 무관하게, 초기 Nonce와 Key에 의해서만 결정되는 의사난수 비트 스트림($O_j$)을 생성하여 평문과 XOR 한다
      - **오류 복구력 우수**: 이 독립성 때문에 통신 중 암호문 $C_j$에 전송 오류가 발생해도, 해당 위치로 평문 $P_j$ 비트만 깨질 뿐 다음 블록의 복호화에는 전혀 영향을 주지 않는다
    - 단점: OFB는 메시지 스트림 수정 공격에 대해 CFB보다 더 취약함
---
- **기말 출제** CTR(Counter Mode) 








