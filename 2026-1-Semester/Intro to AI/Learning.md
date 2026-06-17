# [인공지능개론] 5주차 학습 노트: 학습(Learning)의 이해 
**가천대학교 스마트보안전공 기말고사 대비 및 GitHub 정리용**

---

## 1. 기계 학습(Machine Learning)의 개요 및 지도 학습(Supervised Learning)

기계 학습은 주어진 데이터 세트(입력-출력 쌍)를 바탕으로, 새로운 입력값을 출력값으로 매핑하는 함수(Function)를 학습하는 과정입니다.

* **지도 학습(Supervised Learning):** 입력-출력 쌍으로 구성된 데이터 세트(정답이 있는 데이터)가 주어졌을 때, 입력값을 출력값에 매핑하는 함수를 학습하는 방법론입니다. 
* **분류(Classification):** 지도 학습의 대표적인 예로, 입력된 데이터 포인트를 이산적인(Discrete) 카테고리로 매핑하는 함수를 학습하는 작업입니다.

**[예시 데이터 및 산점도(Scatter Plot) 해설]**
교안에서는 날짜별 습도(Humidity)와 기압(Pressure) 데이터를 통해 비가 왔는지(Rain), 안 왔는지(No Rain)를 분류하는 예시를 보여줍니다.
* 함수 정의: $f(\text{humidity}, \text{pressure}) = \text{Rain or No Rain}$
* **그림 해설:** 2차원 그래프에서 X축은 습도(Humidity), Y축은 기압(Pressure)을 나타냅니다. 파란색 원은 '비가 오지 않음(No Rain)', 빨간색 원은 '비가 옴(Rain)'을 의미하는 데이터 포인트입니다. 우리의 목표는 이 파란색 원과 빨간색 원을 잘 나누는 분류 기준(함수)을 찾는 것입니다.

*(참고: 교안 1p ~ 13p)*

---

## 2. 분류 알고리즘 (Classification Algorithms)

### 2.1. 최근접 이웃(Nearest-Neighbor) 및 k-NN 분류
* **Nearest-Neighbor:** 입력값이 주어졌을 때, 가장 가까운 거리에 있는 기존 데이터 포인트의 클래스를 그대로 선택하는 직관적인 알고리즘입니다.
* **k-Nearest-Neighbor (k-NN):** 데이터가 많을 때 사용하는 방법으로, 입력값과 가장 가까운 k개의 데이터 포인트를 찾고, 그중 가장 많이 등장한(가장 흔한) 클래스를 다수결로 선택합니다.

*(참고: 교안 14p ~ 19p)*

### 2.2. 결정 경계(Decision Boundary)와 퍼셉트론(Perceptron)
데이터를 두 집단으로 나누기 위해 선형 선(Decision Boundary)을 긋는 과정입니다.

* **수식화:** 습도를 $x_1$, 기압을 $x_2$라고 할 때, 가중치(Weight) $w_0, w_1, w_2$를 사용하여 선형 결합을 만듭니다.
    $$w_0 + w_1 x_1 + w_2 x_2 \ge 0$$
    위 조건을 만족하면 'Rain', 그렇지 않으면 'No Rain'으로 결정합니다.
* **내적(Dot Product) 표현:** 가중치 벡터 $w$와 입력 벡터 $x$의 내적으로 간단히 표현할 수 있습니다.
    $$h_w(x) = 1 \text{ (if } w \cdot x \ge 0 \text{), } 0 \text{ (otherwise)}$$
* **퍼셉트론 학습 규칙(Perceptron Learning Rule):** 주어진 데이터 포인트 $(x, y)$에 대해 가중치를 업데이트하는 규칙입니다. 예측값과 실제값의 차이(Error)를 구하여, 임의의 학습률(Learning rate, $\alpha$)만큼 가중치를 조정합니다.
    $$w_i = w_i + \alpha (y - h_w(x)) x_i$$
* **임계 함수(Threshold Function) 그림 해설:**
    * **Hard threshold:** $w \cdot x$가 0보다 작으면 0, 0보다 크면 1로 딱 떨어지는 계단형 그래프입니다. 미분이 불가능하다는 단점이 있습니다.
    * **Soft threshold:** 계단 형태를 부드러운 곡선(Sigmoid 형태)으로 만든 그래프입니다. 미분이 가능하여 기울기를 활용한 역전파 학습에 유리합니다.

*(참고: 교안 20p ~ 30p)*

### 2.3. 서포트 벡터 머신 (Support Vector Machines, SVM)
단순히 두 카테고리를 가르는 선을 넘어서, **최대 마진(Maximum Margin)**을 가지는 결정 경계를 찾는 알고리즘입니다.
* **그림 해설:** 파란색 점 집단과 빨간색 점 집단 사이를 지나는 여러 직선 중, 두 집단과의 거리(여백, Margin)가 가장 넉넉한(넓은) 최적의 중앙선을 찾는 과정을 보여줍니다. 이를 통해 새로운 데이터에 대한 일반화 성능을 높입니다.

*(참고: 교안 31p ~ 37p)*

---

## 3. 회귀 (Regression) 및 가설 평가 (Evaluating Hypotheses)

### 3.1. 회귀 (Regression)
입력 포인트를 '연속적인 값(Continuous value)'으로 매핑하는 지도 학습의 또 다른 형태입니다. 독립변수와 종속변수 간의 상관관계를 파악하고 예측할 때 사용합니다.
* **그림 해설:** X축이 광고비(Advertising), Y축이 판매량(Sales)인 산점도에서, 데이터 포인트들의 전반적인 추세를 가장 잘 나타내는 하나의 직선($y = ax + b$)을 긋는 과정을 보여줍니다. 이를 통해 특정 광고비에 대한 판매량을 예측할 수 있습니다.

*(참고: 교안 38p ~ 41p)*

### 3.2. 손실 함수 (Loss Function)
우리가 설정한 가설(모델)이 실제 데이터와 비교해 얼마나 성능이 떨어지는지(Poorly performs)를 오차(Error) 수치로 나타내는 함수입니다.

* **0-1 Loss Function:** 실제값(Actual)과 예측값(Predicted)이 같으면 0, 다르면 1을 반환합니다. 정확히 몇 개 틀렸는지는 알 수 있으나 정도의 차이를 알 수 없습니다.
* **L1 Loss Function (Absolute Error):** 실제값과 예측값의 차이에 절댓값을 씌웁니다.
    $$L(\text{actual}, \text{predicted}) = |\text{actual} - \text{predicted}|$$
    * **그림 해설:** 실제 데이터 포인트(파란 원)와 예측 직선 사이의 수직 거리(오차)를 절댓값 길이 그대로 나타낸 선분들이 보여집니다.
* **L2 Loss Function (Squared Error):** 실제값과 예측값의 차이를 제곱합니다. 오차가 클수록 패널티를 기하급수적으로 크게 줍니다.
    $$L(\text{actual}, \text{predicted}) = (\text{actual} - \text{predicted})^2$$

*(참고: 교안 42p ~ 50p)*

---

## 4. 과적합(Overfitting)과 일반화(Generalization)

### 4.1. 과적합 (Overfitting)
모델이 훈련 데이터 세트에 너무 과하게 맞춰져서(Fits too closely), 처음 보는 미래의 새로운 데이터(Test Data)에 대해서는 제대로 예측하지 못하고 일반화(Generalize)에 실패하는 현상입니다.
* **그림 해설 (Overfitting):** 판매량 산점도에서 모든 점을 하나하나 다 지나가도록 구불구불하게 억지로 이은 노란색 선을 보여줍니다. 이는 현재 데이터에는 오차가 0이지만, 새로운 데이터가 들어왔을 때는 전혀 맞지 않는 쓸모없는 모델이 됩니다.

### 4.2. 정규화 (Regularization)
과적합을 방지하기 위해, 가설(모델)이 너무 복잡해지는 것에 불이익(Penalty)을 주고 더 단순하고 일반적인 가설을 선호하도록 유도하는 기법입니다.
$$cost(h) = loss(h) + \lambda \times complexity(h)$$
학습할 때 오차(loss)뿐만 아니라 모델의 복잡도(complexity)까지 최소화하도록 목표를 설정합니다.

### 4.3. 교차 검증 (Cross-Validation)
데이터 수집이 어려울 때, 가진 데이터를 효율적으로 나누어 모델을 평가하는 방법입니다.
* **Holdout Cross-Validation:** 전체 데이터를 학습용(Training set)과 평가용(Test set)으로 단순히 분리하여 검증합니다.
* **k-fold Cross-Validation:** 전체 데이터를 k개의 세트로 쪼갠 후, 한 세트를 테스트용으로 쓰고 나머지 k-1개를 학습용으로 쓰는 과정을 총 k번 반복하여 평균을 냅니다. 과적합 방지에 매우 효과적입니다.

*(참고: 교안 51p ~ 60p)*

---

## 5. 강화 학습 (Reinforcement Learning)

강화 학습은 정답 대신 **보상(Reward)이나 처벌(Punishment)**이 주어지는 환경에서, 에이전트(Agent)가 미래에 어떤 행동(Action)을 취해야 가장 큰 이득을 얻을지 학습하는 방법입니다.

### 5.1. 에이전트-환경 상호작용 (Agent-Environment Loop)
* **그림 해설:** 에이전트(Agent)가 환경(Environment) 속에서 특정한 행동(Action)을 취합니다. 그러면 환경은 에이전트에게 새로운 상태(State)와 그에 따른 보상(Reward)을 반환합니다. 이 순환 고리를 통해 학습이 진행됩니다.

### 5.2. 마르코프 결정 과정 (Markov Decision Process, MDP)
의사결정을 수학적으로 모델링한 것으로, 상태(States), 행동(Actions), 보상(Rewards)을 포함합니다.
* Set of states $S$
* Set of actions $ACTIONS(s)$
* Transition model $P(s' | s, a)$
* Reward function $R(s, a, s')$

**[그림 해설: Grid World]**
격자(Grid) 맵에서 노란색 원(Agent)이 시작점, 초록색 사각형(Goal)이 최종 도달 목표입니다. 도달 시 큰 긍정적 보상(Positive Reward)을 얻습니다. 반면 빨간색 사각형은 페널티(Penalty) 구역으로, 진입 시 점수가 깎입니다. 에이전트는 보상을 최대화하는 최적의 경로를 찾아야 합니다.

*(참고: 교안 61p ~ 74p)*

### 5.3. Q-러닝 (Q-learning)
상태 $s$에서 행동 $a$를 취했을 때 얻을 수 있는 가치의 추정치인 $Q(s, a)$ 함수를 학습하는 방법입니다.

* **학습 과정:**
    1. 모든 $s, a$에 대해 $Q(s, a) = 0$으로 초기화합니다.
    2. 상태 $s$에서 행동 $a$를 취하고 보상 $r$을 관찰할 때마다 아래 수식으로 $Q$값을 업데이트합니다.
    $$Q(s, a) \leftarrow Q(s, a) + \alpha \left( (r + \gamma \max_{a'} Q(s', a')) - Q(s, a) \right)$$
    (현재 얻은 보상 $r$과 다음 상태 $s'$에서 얻을 수 있는 최대 기대 보상의 합을 기반으로 기존 $Q$값을 수정합니다.)

* **탐험(Explore) vs. 활용(Exploit):** 당장 눈앞의 보상이 높은 행동만 반복(Exploit)하다 보면 최적의 경로를 놓칠 수 있습니다. 따라서 가끔은 무작위로 다른 행동을 시도(Explore)해 보아야 합니다.
    * **$\epsilon$-greedy 알고리즘:** 일부러 확률 $\epsilon$만큼 무작위(Random) 행동을 선택하고, $1-\epsilon$의 확률로는 현재까지 학습된 최적의 행동을 선택하는 방식입니다.

* **적용 예시 (그림 해설):**
    체스 게임, 유전 알고리즘을 통한 신경망 진화(돌연변이율 적용), 주식 트레이딩 차트 예측, 둠(Doom) 게임 인공지능 등 다양한 환경에 적용할 수 있습니다. 상태 공간이 너무 클 때는 Q값을 일일이 저장하기 어려워 함수 근사(Function approximation)를 사용하기도 합니다.

*(참고: 교안 75p ~ 94p)*

---

## 6. 비지도 학습 (Unsupervised Learning)

비지도 학습은 정답이나 추가적인 피드백 없이, **순수한 입력 데이터 자체의 패턴과 구조**를 학습하는 방법론입니다.

### 6.1. 군집화 (Clustering)
객체들의 집합을 분석하여, **서로 비슷한 성질을 가진 객체들끼리 같은 그룹(Cluster)으로 묶는 작업**입니다.
* **활용 분야:** 유전자 연구, 이미지 분할(Image segmentation), 시장 조사(Market research), 의료 영상, 소셜 네트워크 분석 등.

### 6.2. k-means Clustering
가장 대표적인 군집화 알고리즘으로, 데이터를 무작위로 k개의 군집으로 나눈 후, 각 군집의 중심점(Center)을 계산하고, 데이터를 가장 가까운 중심점의 군집으로 재할당하는 과정을 중심점이 변하지 않을 때까지 반복(Repeatedly assigning & updating)하는 방식입니다.

*(참고: 교안 95p ~ 106p)*
