- 숨기는 암호를 최소화 하자.
  1. 표준 암호 "키"만 숨기자.
  2. 공개키 암호 시스템: Diffie-Hellman Key Exchange (대칭키 알고리즘, 1976)

- b가 a를 나눈다 = b|a
- The Division Algorithm
  양의 정수 n, 음이 아닌 정수 a
  a = qn + r, 0 <= r < n; q = [a/n] (바닥함수)
- The Euclidean Algorithm (p. 49) => 확장 유클리드 알고리즘 => 곱셈에 대한 역원 계산
  : gcd(최대공약수) 계산하는 절차.
  gcd(a, b) = gcd(-a, b) = .. = gcd(|a|, |b|)
  a, b > 0 -> gcd(a, b), a = bq1 + r1
            = gcd(b, r1), b = rq2 + r2
            = gcd(r1, r2)

-The Modulus (p. 51)
 a mod n <=> a를 n으로 나눈 나머지
 a = qn + r, 0 <= r < n; q = [a/n] (바닥함수)
   = [a/n] * n + (a mod n)
주의) a = b mod n != a 동치(합동) b(mod n)

- Ex) {modulo 8} 예) 9 = 1 (p. 53)
  + -> 항등원: 0
  * -> 항등원: 1
  a의 덧셈의 역원: -a(일반적인 경우) -> 8 - a
  a의 곱셈의 역원: 1/a -> 없을수도
  - 무한(I) => modulo(F) => 유한(O)

- Zn = {0, 1, .., n-1}
- p. 55 예시 성립 -> 서로소
  a * b = 1 (mod n) (b존재 -> a, n 서로소)
