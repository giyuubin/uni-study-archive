- Uncertainty
  - Probability of Possible Worlds
    - ω
    - P(ω): 확률을 표현
    - 0≤P(ω)≤1: 각각의 가능한 세계가 발생할 확률은 항상 0(발생X)과 1(무조건 발생) 사이의 값이다
    - <img width="166" height="33" alt="image" src="https://github.com/user-attachments/assets/340c0a45-88cb-4b53-9e3b-e91af692ea0b" />
      : 세상에서 발생할 수 있는 모든 가능한 세계들의 확률을 합치면 반드시 1(100%)이 되어야 함
    - Unconditional probability(무조건부 확률): 다른 어떠한 전제 조건이나 관측치에 영향을 받지 않고, 특정 사건이 전체 표본 공간에서 독립적으로 발생할 본연의 확률을 의미함
    - Conditional probability(조건부 확률): 어떤 사건이나 증거가 주어졌다는 전제하에, 알고자하는 다른 사건(Query)이 발생할 확률을 의미함
      - P(a∣b): 사건 b가 주어졌을 때(조건), 사건 a가 일어날 확률
      - P(a∣b) = P(a∧b) / P(b) // 따라서 P(a∧b) = P(a∣b)P(b)
    - Random variable(확률 변수): 불확실한 환경 속에서 여러 가지 state나 값을 가질 수 있으며, 각 상태마다 확률이 할당되는 변수를 의미함
      - Ex: Weather: {sun, cloud, rain, wind, snow}
    - Probability distribution(확률 분포): 특정 확률 변수가 가질 수 있는 모든 가능한 상태들에 대하여, 각각의 상태가 발생할 확률값을 할당해 나열한 것
      - Ex: Flight: {on time, delayed, cancelled}
        - P(Flight = on time)=0.6
        - P(Flight = delayed)=0.3
        - P(Flight = cancelled)=0.1
        - P(Flight)=⟨0.6,0.3,0.1⟩ // 튜플 표기
    - independence(독립 시행): 하나의 사건 발생했다는 지식이 다른 사건이 발생할 확률에 아무런 영향을 미치지 않는 성질
      - 종속적인 경우: P(a∧b)=P(a)P(b∣a)
      - 독립성이 성립할 때의 경우: P(a∧b)=P(a)P(b) // 사건 a는 b에 아무런 영향을 주지 않음
        - 서로 다른 주사위 굴릴 때, 한 주사위에서 다른 숫자 나올 때(0)
  - **Bayes's Rule**(베이즈 정리)
    - visible effect를 바탕으로, 그것을 발생시킨 숨겨진 원인의 확률 역추적하여 계산하는 것
    - P(a∧b)=P(b)P(a∣b), P(a∧b)=P(a)P(b∣a)
    - 따라서 P(a)P(b∣a)=P(b)P(a∣b)
    - P(b∣a) = P(a∣b)P(b) / P(a)
      - P(b∣a): 알고자 하는 목표 // visible effect a가 관측되었을 떄, 숨겨진 원인 b일 확률)
      - P(a∣b): 이미 알고 있는 지식
      - P(b): 숨겨진 b의 무조건부 확률
      - P(a): 관측된 증거 a의 무조건부 확률
    - Ex: Given clouds in the morning, what's the probability of rain in the afternoon?
      - 80% of rainy afternoon start with cloudy mornings
      - 40% of days have cloudy mornings
      - 10% of days have rainy afternoons
      - sol: P(rain∣clouds) = P(clouds∣rain)P(rain) / P(clouds) = 0.2
  - Joint probability(결합 확률):두 개 이상의 확률 변수가 각각 특정한 상태를 동시에 가질 확률
    - Ex: P(C=cloud)를 구하기 위해, P(C=cloud,R=rain)과 P(C=cloud,R=¬rain)의 값인 0.08+0.32를 합산하여 최종적으로 0.40을 도출
  - Probability rules
    - Negation(부정): P(¬a)=1−P(a)
    - Inclusion-Exclusion(포함-배제): P(a∨b)=P(a)+P(b)−P(a∧b)
    - Marginalization(주변화): P(a)=P(a,b)+P(a,¬b) // 알고자 하는 특정 단일 변수만의 무조건부 확률 추
    - Conditioning(조건화): P(a)=P(a∣b)P(b)+P(a∣¬b)P(¬b)
  - Bayesian networks: 확률 변수들 사이에 존재하는 **의존성**을 시각적이고 수학적으로 나타내는 data structure
    - 














