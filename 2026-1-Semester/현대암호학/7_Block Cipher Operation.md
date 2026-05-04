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
- Electronic CodeBook
  - Mode of 









