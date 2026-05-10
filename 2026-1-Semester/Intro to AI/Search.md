- Search (모든 문제에는 시작과 끝이 존재)
  - agent: 문제를 해결하려는 주체
  - state: agent와 그를 둘러싼 환경의 구성
  - Search Problems 5가지 핵심 요소
    - **Initial state**: 에이전트가 탐색을 시작하는 첫 상태
    - Actions: 특정 state에서 선택할 수 있는 행동들의 집합 (Actions(s))
    - Transition model: 특정 state에서 어떤 action을 했을 때, 그 결과로 도달하게 되는 다음 state를 설명하는 모델 (RESULT(s, a))
    - Goal test: 주어진 state가 도달하고자 하는 Goal state인지 확인하는 과정
    - Path cost function(경로 비용 함수): 주어진 path를 통과하는 데 드는 Numerical cost를 계산한다
  - Solution: initial state에서 goal state로 이끄는 Sequence of action(행동들의 순서)
  - Optimal solution: all solution중 lowest path cost를 의미함
  - Node: 탐색을 위해 기록을 유지하는 data structure
    - a state
    - a parent(부모노드)
    - an action
    - a path cost
  - Approach
    - Frontier: 확장되어지기를 기다리는 leaf node
    1. initial state를 **포함**하는 frontier를 시작으로 생성
    2. 반복
       - frontier가 비어있다면, No solution 반환
       - frontier에서 node 하나를 꺼냄
       - 해당 node가 goal state를 포함하는지 검사하고, 맞다면 solution 반환
       - goal state가 아니라면 node를 확장하고, 결과로 생성된 노드들을 프론티어에 추가한다
    - ISSUES!
      - 위 기본 접근 방식을 사용하면 agent는 무한 루프에 빠질 수 있다
      - frontier에서 node를 탐색할 때 특정 경로들(예: A -> B, B -> A)이 계속 반복되는 상황 발생 가능
      - 기존 방식은 이미 방문했던 노드를 기억하는 장치가 없음
      - 따라서 frontier에 동일한 노드가 영원히 번갈아 추가되며 탐색이 안 끝남
  - Revised Approach(**중복 검사 여부**)
    - Explored set 도입
    1. initial state를 **포함**하는 frontier를 생성한다
    2. 비어있는 explored set을 함께 시작한다
    3. 반복
       - frontier가 비어있다면 no solution 반환
       - frontier에서 node 하나를 꺼냄
       - 해당 node가 goal state라면 solution 반환
       - **꺼낸 node를 explored set에 추가**
       - node를 확장하여 새로운 결과 node들을 생성하되,
         해당 node들이 frontier나 explored set에 존재하지 않는 경우에만 frontier에 추가
    - stack: LIFO(Last-In First-Out) 원리를 따르는 데이터 타입
  - Depth-First Search(DFS): 가장 깊은 노드를 항상 우선적으로 확장하는 알고리즘
    - 반복(추가)
      - 새로 생성된 node들이 frontier와 explored set 두 곳 모두에 존재하지 않을 경우에만 frontier에 추가한다
      - **stack(LIFO) 구조**이므로 새로 들어온 node들이 frontier의 최상단에 위치한다
  - Breadth-first search(BFS): 가장 얕은 노드를 우선적으로 확장하는 알고리즘
    - Queue: FIFO(First-In First-Out) 원리를 따르는 데이터 타입
    - 반복
      - 새로 생성된 node들이 frontier와 explored set 두 곳 모두에 존재하지 않을 경우에만 frontier에 새롭게 추가한다
      - Queue(FIFO) 구조이므로 새로 들어온 node들은 frontier의 맨 뒤에 위치한다
  - Uninformed search(정보가 없는 탐색)
    - Depth-First Search(DFS)
    - Breadth-first search(BFS)
  - Informed search(정보가 있는 탐색)
    - Greedy Best-First Search(GBFS):
      - 탐욕적 최고 우선 탐색
      - Heuristic function(휴리스틱 함수)에 의해 goal가 가장 가깝다고 추정되는 node를 최우선으로 확장한다
      - $h(n)$ (휴리스틱 함수): 현재 노드 $n$에서 goal state까지 도달하는데 걸릴 것으로 예상되는 추정 비용을 의미함 (두 좌표간 거리(Manhattan distance), 벽을 없앰)
      - GBFS는 **이 수식의 결과값 하나에만 전적으로 의존하여** 탐색 방향을 결정한다 (분기점: MD 더 작은 쪽 선택)
      - Optimality 결여
        - Greedy(탐욕적)이라는 닉 값에 맞게 당장 가장 가까운 길만 쫓음
        - 운이 좋으면 빠르게 해를 찾지만, 전체 경오 비용 관점에서는 그 해가 Optimal solution이 아닐 확률이 높다
    - A* search
      - frontier에서 node를 확장할 때 goal까지의 예상 비용뿐만 아니라 시작점부터 현재까지 이동한 실제 비용까지 종합적으로 고려하여 optimal 경로를 찾는 알고리즘
      - 평가 함수의 합산 값이 가장 낮은 node를 최우선으로 확장한다
      - $g(n)$: 시작 state에서 현재 node n까지 도달하는 데 실제로 소모된 비용
      - $h(n)$: 현재 node n에서 goal까지 도달하는 데 걸릴 것으로 추정되는 예상 비용 (휴리스틱 함수)
      - 평가 함수: $f(n) = g(n) + h(n)$이 가장 낮은 노드를 선택하여 확장
      - $h(n) ≤ h(n') + c$: node $n$에서 다음 node $n'$으로 갈 때 드는 실제 행동 비용을 $c$라고 할 때 만족해야
        - Ex: 1 + 16에서 1은 시작점부터 한 칸을 이동한 실제 비용 $g(n)$을 의미하고, 16은 MD로 산출한 예상 비용 $h(n)$을 의미함
    - 비교
      - GBFS는 실제 소요 비용 $g(n)$은 무시하고 오직 $h(n)$값만으로 탐색하지만, A* 탐색은 $g(n)$과 $h(n)$을 모두 더해 합리적으로 경로를 평가한다.
  - Adversarial Search(적대적 탐색)
    - 틱택토, 체스 바둑 등
    - 나와 목표가 반대인 상대방이 존재하는 환경에서 최적의 수를 찾기 위한 탐색 기법
    - Minimax
      - MAX(X)는 자신의 점수를 최대화하려고 함(승리: 1)
      - MIN(O)은 자신의 점수를 최소화하려고 함(승리: -1)
      - Game
        - $S_0$: initial state
        - PLAYER(s): 현재 state s에서 차례를 진행할 플레이어를 반환
        - ACTION(s): 현재 state s에서 플레이어가 선택할 수 있는 합법적인 행동들의 집합
        - RESULT(s, a): state s에서 return a를 취했을 때 도달하게 되는 다음 state
        - TERMINAL(s): state s가 게임이 끝난 Terminal state(종료 상태)인지 여부 확인 (true/false)
        - UTILITY(s): terminal state s에 대한 최종 점수를 return (누가 이겼는지 1/-1)
      - Ex: MIN은 상대가 어디에 둬야 이기는지를 고려한다 (value -> 선택)
      - Algorithm
        1. 종료 검사: 현재 state가 TERMINAL(state)인지 확인한다. 맞다면 UTILITY(state)값을 반환 // 승패가 끝났는가
        2. MAX-VALUE:
           - 최대값을 찾기 위해 **기준값 $v$를 −∞로** 초기화 // 높은 값 비교를 위해 충분히 작은 값 설정
           - 가능한 모든 ACTIONS에 대해, 결과 상대로 MIN-VALUE(RESULT(state, action))를 호출하여 **더 큰 놈 선택**
        3. MIN-VALUE:
           - 최소값을 찾기 위해 기준값 $v$를 ∞로 초기화
           - 가능한 모든 ACTIONS에 대해, 결과 상대로 MAX-VALUE(RESULT(state, action))를 호출하여 **더 작은 놈 선택**
  - Optimizations
    - Alpha-Beta pruning: Minimax tree를 탐색하는 과정에서, 최종적인 결정에 아무런 영향을 주지 않는 불필요한 나뭇가지를 탐색에서 제외(pruning)하여 연산 속도를 높이는 최적화 기법
    - Depth-Limited Minimax: 제한된 시간이나 연산 자원 때문에 게임 트리의 끝까지 탐색할 수 없을 떄, 미리 정해둔 깊이까지만 탐색하고 중단하는 기법
    - Evaluation Function: 주어진 상태로부터 기대되는 게임의 효용 가치를 추정하는 함수






