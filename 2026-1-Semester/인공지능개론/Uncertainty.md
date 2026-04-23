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
    - P(X∣Parents(X)): 네트워크 내의 각 노드 X가 가지는 고유한 조건부 확률 분포이다. 즉, 자신에게 화살표를 보내는 부모 노드들의 상태가 주어졌을 때, 자식 노드인 X가 특정 상태를 가질 확률을 뜻한다
    - 상위노드가 없다 -> root node -> 조건부 확률X
      - Ex: 부모가 없는 최상위 노드인 Rain 옆에는 {none: 0.7, light: 0.2, heavy: 0.1} 이라는 단순한 '무조건부 확률표'가 붙어 있음
    - Ex: P(light,no,delayed,miss)=P(light)×P(no∣light)×P(delayed∣light,no)×P(miss∣delayed)
      - X→Y 화살표는 X가 Y의 원인이 되는 부모(Parent)임을 의미
  - Inference: (확률 기반)추론 => 어느 쪽에 더 근접한지
    - Query X: 분포를 계산하고자 하는 목표 질의 변수
    - Evidence variable E: 관측된 증거 변수
    - Hidden variables Y: non-evidence(보이진 않지만, 존재함), non-query 변수
    - Goal: Calculate P(X|e) // 조건부 확률의 분포가 아닌 보이는 e로부터 새로운 사실 도출
    - <img width="426" height="66" alt="image" src="https://github.com/user-attachments/assets/363f64b7-689a-407d-b7c2-e11d37daebd3" />
  - Approximatae Inference(근사 추론)
    - Sampling: 베이지안 네트워크의 조건부 확률들을 바탕으로, 변수들에 무작위 값을 할당하여 하나의 구체적인 가능한 세계를 계속해서 만들어내는 과정 // 초기에 계산을 해둔다
    - Rejection sampling: 무작위로 샘플을 만들다가 관측된 증거와 일치하지 않으면 그냥 버리는 방식. 하지만 증거가 발생할 확률이 매우 낮으면 샘플을 버리는 비율이 높아 비효율적
    - Likelihood Weighting: rejection sampling의 비효율성을 극복하기 위해, 버려지는 샘플 없이 처음부터 증거 변수를 고정해놓고 샘플링 진행한 뒤, 해당 샘플이 생성해 낸 증거 발생 확률을 **가중치**로 곱하여 보정한다
      - 작동 원리
        1. 증거 고정: 관측된 증거인 Train=on_time을 가장 먼저 샘플에 고정 // 버려지는 샘플을 만들지 않기 위함
        2. 은닉 변수 샘플링: 네트워크의 위에서부터 아래로 내려오며 증거가 아닌 변수들을 확률표에 따라 무작위 추출
           - Rain 노드: P(Rain) 표 {none: 0.7, light: 0.2, heavy: 0.1} 에 따라 주사위를 굴립니다. 가령 R=light 가 뽑혔다고 가정
           - Maintenance 노드: 부모가 R=light 로 정해졌으므로, P(M ∣ R=light) 인 {yes: 0.2, no: 0.8} 표에 따라 주사위를 굴립니다. 가령 M=yes 가 뽑혔다고 가정
        3. 가중치 부여: 고정해 두었던 증거 변수(Train=on_time)의 '가능도(Likelihood)'를 계산하여 샘플의 가중치로 곱한다
           - 현재까지 뽑힌 샘플의 부모 상태는 R=light,M=yes
           - Train 의 조건부 확률표에서 부모가 R=light,M=yes 일 때 Train=on_time 일 확률을 찾아보면 0.6
           - 따라서 이 샘플은 0.6의 가중치(Weight)를 얻게 됨
           - 마지막으로 남은 Appointment 변수 역시 T=on_time 이라는 부모 조건하에 샘플링(예: A=attend)을 완료
           - 완성된 샘플 [R=light, M=yes, T=on time, A=attend]는 단순한 1개의 샘플로 취급되는 것이 아니라, '0.6개'짜리 가치(Weight)를 지닌 샘플로 최종 취합
  - Uncertainty over time
    - 시간의 흐름에 따라 변화하는 모든 상태를 완벽히 계산하는 것은 불가능
    - 인공지능은 연산량을 줄이기 위해 Assumption(가정)을 도입
    - Markov Assumption: 시스템의 현재 상태는 오직 유한하고 고정된 이전 상태들에만 의존한다고 가정
    - Markov Chain: 각 변수의 분포가 마르코프 가정을 따르는 확률 변수들의 연속적인 시퀀스이다
    - Hidden Markov Model(HMM): 관측 가능한 사건을 생성해 내는 숨겨진 상태를 가진 시스템을 모델링하기 위한 마르코프 모델 // 우산을 들고 교실에 들어온다 -> 아마 비가 올 것이다
      - Filtering: 시작부터 지금까지의 관측치들을 바탕으로, 현재 상태 분포 계산
      - Prediction: 시작부터 지금까지의 관측치들을 바탕으로, 미래 상태 분포 계산
      - Smoothing: 시작부터 지금까지의 관측치들을 바탕으로, 과거 상태 분포 계산
      - Most likely explanation: 시작부터 지금까지의 관측치들을 바탕으로, 가장 가능성 높은 상태들의 전체 순서 계산
    - Sensor Markov Assumption: 특정 증거(관측치) 변수는 오직 그 관측이 발생한 해당 시점의 상태에만 의존한다는 가정















