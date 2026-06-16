# 📖 인공지능 프로그래밍 Week 10: 앙상블 학습의 진화 (GBM, XGBoost, LightGBM)

이 문서는 Gradient Boosting Machine(GBM)의 기초부터, 이를 최적화한 XGBoost, 그리고 대용량 데이터 처리에 특화된 LightGBM까지의 발전 과정을 다룹니다.

---

## 1. Gradient Boosting Machine (GBM)의 기본 원리

### 1.1 AdaBoost와의 차이점
기존의 AdaBoost는 분류를 잘못한 **'오답(고가중치 데이터)'**에 초점을 맞추어 다음 모델을 학습시킵니다. 반면, Gradient Boosting Machine(GBM)은 모델의 예측값과 실제값의 차이, 즉 **'잔차(Residual)'**를 예측하는 새로운 모델을 순차적으로 더해가는(Additive) 방식입니다.

### 1.2 잔차(Residual)와 경사 하강법(Gradient Descent)
* **수식적 이해:** GBM에서 다음 트리가 학습해야 할 잔차는 회귀 모형의 Loss Function에 대한 **Negative Gradient(음의 기울기)**와 같습니다.
* **작동 방식:** 손실 함수 $J(w)$의 그래프가 U자 형태라고 할 때, 현재 상태에서 손실을 최소화하는 방향(기울기의 반대 방향)으로 파라미터를 업데이트합니다. 
* **반복 학습의 시각화 (도표 해설):**
    * 교안의 `Height vs Weight` 산점도 그래프에서, 파란 점과 빨간 점 사이의 검은 선이 초기 회귀선입니다. 각 데이터 포인트에서 선까지의 수직선(틀린 부분)이 잔차입니다.
    * **Iteration 진행 과정:** Iteration 1에서는 예측선이 실제 데이터 분포(Sine 함수 형태)를 잘 반영하지 못하고 잔차가 큽니다. 하지만 Iteration 2, 3을 거쳐 Iteration 50에 도달하면, 모델의 예측선(빨간 선)이 실제 데이터 분포 곡선에 완벽하게 일치하게 되며, 잔차(Residuals vs x 그래프의 초록색 점들)는 0에 가깝게 평형을 이룹니다.

*(참고: 교안 1p ~ 9p)*

---

## 2. 손실 함수 (Loss Functions)의 종류

GBM이 해결하려는 문제의 종류(회귀 vs 분류)에 따라 손실 함수가 달라집니다. 도표로 제시된 함수들의 형태와 수식적 특징은 다음과 같습니다.

### 2.1 회귀(Regression)용 손실 함수
* **Squared loss ($L_2$):** $\Psi(y,f)_{L_2} = \frac{1}{2}(y-f)^2$
    * 그래프 형태: 부드러운 U자형 2차 곡선 (Parabola).
* **Absolute loss ($L_1$):** $\Psi(y,f)_{L_1} = |y-f|$
    * 그래프 형태: 0을 기준으로 뾰족한 V자 형태. 이상치(Outlier)에 덜 민감합니다.
* **Huber loss:**
    * 오차가 임계값 $\delta$보다 작으면 $L_2$ Loss(2차 함수 형태)를 따르고, 크면 $L_1$ Loss(직선 형태)를 따릅니다. 바닥은 둥글고 양 끝은 뻗어나가는 형태로, $L_1$과 $L_2$의 장점을 결합했습니다.
* **Quantile loss:** $\alpha$ 값에 따라 비대칭적인 V자 형태를 가집니다. 분위수를 예측할 때 사용됩니다.

### 2.2 분류(Classification)용 손실 함수
* **Bernoulli loss:** $\Psi(y,f)_{Bern} = \log(1 + \exp(-2\bar{y}f))$
    * 그래프 형태: $yf$ 값이 양수(정답)로 커질수록 0에 수렴하는 완만한 곡선입니다.
* **AdaBoost loss:** $\Psi(y,f)_{Ada} = \exp(-\bar{y}f)$
    * 그래프 형태: 지수적으로 감소하는 곡선으로, 오답일 때 손실값이 Bernoulli보다 훨씬 급격하게 증가합니다.

*(참고: 교안 10p ~ 12p)*

---

## 3. 과적합 방지 (Regularization) 및 변수 중요도

### 3.1 Overfitting 극복 방법
GBM은 훈련 데이터의 노이즈까지 완벽하게 학습해버리는 과적합(Overfitting) 위험이 큽니다. 이를 방지하기 위해 다음 기법을 사용합니다.
1.  **Subsampling:** 전체 데이터 100%를 쓰지 않고, 일부(예: 80%)만 무작위로 추출하여 다음 트리를 학습합니다.
2.  **Shrinkage (학습률):** 트리를 결합할 때 예측값에 작은 가중치(Learning rate)를 곱하여 반영 속도를 늦춥니다.
3.  **Early Stopping:** Validation 에러가 줄어들지 않으면 학습을 조기 종료합니다.

### 3.2 변수 중요도 (Variable Importance)
* **Influence(T):** 단일 트리 $T$에서 특정 변수 $j$가 얼마나 중요한지를 나타냅니다. 노드를 분할할 때 얻은 **Information Gain (IG)**의 합산으로 계산합니다.
* 전체 GBM 모델에서의 중요도는 모든 트리 $M$개의 Influence 평균값으로 도출됩니다.

*(참고: 교안 13p ~ 17p)*

---

## 4. XGBoost: 확장 가능하고 극대화된 트리 부스팅

XGBoost는 기존 GBM을 시스템적으로, 수학적으로 최적화한 알고리즘입니다.

### 4.1 XGBoost의 핵심 장점 (아키텍처 도식화)
교안의 육각형 다이어그램에 나타난 XGBoost의 6가지 핵심 기능입니다.
1.  **Cache awareness:** 메모리 캐시를 고려한 하드웨어 최적화
2.  **Tree pruning:** 깊이 우선 탐색(Depth-first)을 통한 트리 가지치기
3.  **Parallelized tree building:** 병렬 처리를 통한 학습 속도 극대화
4.  **Regularization:** 과적합 방지를 위한 정규화 수식 자체 내장
5.  **Efficient handling of missing data:** 결측치 자동 처리 알고리즘 내장
6.  **In-built cross-validation:** 내장된 교차 검증 기능
* **성능 결과:** AUROC 0.9662라는 높은 예측력을 보이면서도, 훈련 시간은 기존 GBM(2,069초) 대비 압도적으로 빠른 24초를 기록했습니다.

*(참고: 교안 18p ~ 20p)*

---

## 5. XGBoost의 데이터 분할(Split Finding) 알고리즘

노드를 어떻게 나눌지 결정하는 탐색 알고리즘이 속도의 핵심입니다.

### 5.1 Exact Greedy Algorithm vs Approximate Algorithm
* **Exact Greedy Algorithm:** 모든 가능한 데이터 분할 지점을 전부 테스트하여 최적의 분기를 찾습니다. 정확하지만, 데이터가 메모리에 다 들어가지 않으면 사용할 수 없을 만큼 느립니다.
* **Approximate Algorithm (분위수 버킷팅):** 데이터를 오름차순으로 정렬한 뒤, 백분위수에 따라 10개 등의 버킷(Bucket)으로 나눕니다. 개별 데이터가 아닌 **버킷 단위로 Gradient를 계산하여 최적의 분기점**을 찾습니다. 연산량이 획기적으로 감소합니다.
    * **Global Variant:** 트리 전체에 대해 한 번만 분할 후보를 결정합니다.
    * **Local Variant:** 매 스플릿마다 분할 후보를 다시 결정합니다. 트리 깊이가 깊어질 때 유리합니다.

### 5.2 Sparsity-aware Split Finding (결측치 처리)
데이터에 빈 값(Missing Value)이나 0이 많은 희소(Sparse) 데이터 상황에 대처하는 방식입니다.
* **결측치 방향 지정 (Default Direction):** 트리를 분할할 때, 결측치들을 통째로 **왼쪽 노드로 보냈을 때의 Information Gain**과 **오른쪽 노드로 보냈을 때의 Information Gain**을 각각 계산하여 더 높은 쪽을 '기본 방향'으로 설정합니다.

### 5.3 Efficient System Design (CSC와 병렬 처리)
* **Block 구조와 CSC 포맷:** 가장 시간이 많이 걸리는 정렬 작업을 훈련 전에 한 번만 수행하여 Column 단위로 압축 저장(Compressed Column format)합니다.
* 데이터 블록을 메모리에 올려 선형 스캔만으로 병렬 탐색이 가능해집니다.

*(참고: 교안 21p ~ 37p)*

---

## 6. LightGBM: 대규모 데이터를 위한 경량화 부스팅

LightGBM은 XGBoost보다 한 단계 더 나아가 데이터의 수(Instances)와 변수의 수(Features) 자체를 줄여 연산을 가속화합니다.

### 6.1 GOSS (Gradient-based One-sided Sampling)
단순한 무작위 추출이 아닌, Gradient의 크기를 기준으로 데이터를 샘플링하여 훈련 데이터의 수를 줄입니다.
* **Large Gradient (큰 기울기):** 아직 학습이 덜 된 중요 데이터이므로 **상위 $a \times 100\%$ 데이터를 100% 보존(Keep)**합니다.
* **Small Gradient (작은 기울기):** 이미 학습이 잘 된 데이터이므로 **나머지 중 $b \times 100\%$만 무작위 추출(Randomly Drop)**합니다.
* **상수 곱셈 (Amplification):** 원본 데이터의 분포를 왜곡하지 않기 위해, 추출된 작은 기울기 데이터 집합에 $\frac{1-a}{b}$라는 상수를 가중치로 곱해줍니다. 이를 통해 정확도를 유지하면서도 연산량을 대폭 감소시킵니다.

### 6.2 EFB (Exclusive Feature Bundling)
원-핫 인코딩 등 희소성(Sparsity)이 높은 데이터에서는 두 변수가 동시에 0이 아닌 값을 가지는 경우가 거의 없습니다. (Exclusive 상태). 이를 하나의 변수로 묶어 변수의 수를 줄입니다.
* **그래프 색칠 문제 (Graph Coloring):** 변수를 노드(Vertex)로, 변수 간의 충돌(동시에 0이 아닌 경우)을 엣지(Edge)로 그립니다. 충돌이 없거나 일정 컷오프(예: 0.2) 이하인 약한 엣지를 무시하고, 같은 색으로 묶이는 변수들을 하나의 번들(Bundle)로 병합합니다.
* **변수 병합 방식 (오프셋 추가):**
    * 예를 들어, 값의 범위가 0~3인 Feature $x_4$와 0~2인 Feature $x_3$을 병합합니다.
    * 값들이 겹치지 않도록, 두 번째 Feature인 $x_3$의 **0이 아닌 값에 오프셋 3(이전 변수의 최대값)을 더해줍니다.**
    * 결과적으로 $x_3$의 값은 4, 5로 변환되며, $x_4$와 $x_3$는 단 하나의 Feature Column으로 압축됩니다. 트리 분기 시 데이터 손실 없이 완벽하게 원래 값을 추적할 수 있습니다.

*(참고: 교안 38p ~ 54p)*
