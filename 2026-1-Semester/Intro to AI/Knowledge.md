- Knowledge
  - Knowledge-based agents: 내부에 지식의 표현을 갖추고 있으며, 이를 연산하고 조작하여 새로운 사실을 추론해 내는 에이전트
  - Sentence: 지식 표현 언어로 작성된, 현실 세계에 대한 주장이나 사실을 의미함 (C => sentence => 정보표현)
  - Propositional Logic(명제): true/false
    - **Propositional Symbols(1st)**: $P$, $Q$, $R$
    - Logical Connectives
      - ¬ (Not): 부정
      - ∧ (And): 논리곱, 둘 다 참이어야 참
      - ∨ (Or): 논리합, 둘 중 하나라도 참이면 참
      - → (Implication): 함의, $P$이면 $Q$이다
      - ↔ (Biconditional): 필충, $P$와 $Q$가 동치이다
    - Model: 모든 명제 기호에 대하여 참 또는 거짓의 진리값을 할당한 상태
      - Ex: $P$가 참이고 $Q$가 거짓인 세상
    - **Knowledge base**: KB, agent가 이미 알고 있는 sentences들의 집합
    - **Entailment**: α⊨β, 문장 α가 참이 되는 모든 모델에서 문장 β도 반드시 참이 됨을 의미함
    - Inference(추론): agent가 이미 알고 있는 기존의 문장들을 논리적으로 연결하여, 새로운 문장을 도출해내는 과정
  - Inference Algroithms
    - Model Checking (KB ⊨ α)
      1. 상황에 존재하는 모든 명제 기호로 만들 수 있는 가능한 모든 모델(true/false 조합)을 나열한다
      2. 각 모델마다 에이전트가 가진 전체 KB 문장들이 true가 되는지, false가 되는지 확인한다
      3. KB가 true가 되는 모든 모델들을 찾고, 문장 α 역시 모두 true인지 검사한다
      4. KB가 true인 모든 경우에 α도 참 -> agent는 새로운 사실 α를 확정적으로 도출한다
  - Knowledge Engineering
    - 현실 세계의 문제, 규칙, 상황을 AI agent가 이해하고 추론할 수 있도록 명제 논리 기반의 Symbols와 Sentences로 번역하여 KB를 설계하는 과정
    - Ex: Clue
      - Propositional Symbols (1st)
        -  범인 후보(mustard, plum, scarlet), 방(ballroom, kitchen, library), 흉기(knife, revolver, wrench)를 각각 참/거짓을 담는 단일 기호로 정의
      - Logical Sentences
        - 논리합(∨, Or): 범인은 Mustard, Plum, Scarlet 중 한 명이라는 지식을 (mustard ∨ plum ∨ scarlet)으로 표현
        - 함의(→, Implication) 및 부정(¬, Not): "포모나(Pomona)가 슬리데린(Slytherin)에 속한다면, 후플푸프(Hufflepuff)에는 속하지 않는다"라는 지식을 (PomonaSlytherin $\rightarrow$ $\neg$ PomonaHufflepuff)로 수식화
    - Ex: Logic Puzzles(기숙사 배정), Mastermind 등
  - Inference Rules: agent가 Old sentences에 논리적 공식을 적용하여, 참임을 보장하는 New sentences를 연역적으로 이끌어내는 법칙들
    - Modus Ponens(전건 긍정식): α→β 이고 α 이면, β 이다
    - And Elimination(논리곱 제거): α∧β 이면, α 이다 (둘 다 참이므로 각각 하나만 떼어내도 참이다)
    - Double Negation Elimination(이중 부정 제거): ¬(¬α) 이면, α 이다
    - **Implication Elimination**(함의 제거): α→β 이면, **¬α∨β**
    - **Biconditional Elimination**(쌍조건부 제거): α↔β 이면, **(α→β)∧(β→α)** // 중요
    - 드 모르간 법칙:
      - ¬(α∧β) 이면, ¬α∨¬β 이다
      - ¬(α∨β) 이면, ¬α∧¬β
    - 분배법칙:
      - α∧(β∨γ) 이면, (α∧β)∨(α∧γ) 이다
      - α∨(β∧γ) 이면, (α∨β)∧(α∨γ) 이다
  - Theorem Proving as Search
    - initial state: starting **knowledge base**
    - actions: 위의 7가지 추론 규칙들
    - transition model: 규칙 적용 후, New sentences가 추가되어 업데이트된 New knowledge base 상태
    - goal test: 증명하고자 하는 질의 문장이 KB에 도출되었는지 확인 (true/false)
    - path cost function: 증명을 완료하는 데 사용된 논리적 단계의 수
  - **Resolution**(분해): 두 개의 논리식에서 서로 반대되는 진리값($P$와 ¬$P$)을 찾아 상쇄하고, 남은 요소들을 하나로 합쳐 새로운 논리식을 도출해내는 규칙 (**두 개의 state가 충돌하는가?**)
    - P∨Q 와 ¬P 가 주어질 때 ⇒ 결과로 Q 가 도출
    - Clause(절): 여러 리터럴들이 논리합(∨, OR)으로만 연결된 문장
      - P∨Q∨R
    - **Conjunctive Normal Form(CNF)**: 여러 개의 절들이 논리곱(∧, AND)으로 결합되어 있는 논리식의 표준 형태
      - (A∨B∨C)∧(D∨¬E)∧(F∨G)
      - **분해 증명을 수행하려면 반드시 모든 문장이 CNF형태로 변환되어야 한다**
      - **not도 무조건 절 안에 있어야 한다**
      - Conversion to CNF // Resolution 쉽게 => 한 눈에 충돌 확인 가능
        - (α↔β) 를 (α→β)∧(β→α) 로 변환
        - (α→β) 를 ¬α∨β 로 변환
        - ¬(α∧β) 를 ¬α∨¬β 로 변환
        - α∨(β∧γ) 를 (α∨β)∧(α∨γ) 로 변환 // CNF의 정의에 맞게 괄호 안은 ∨로만, 괄호 밖은 ∧로만 묶이도록 ∨ 기호를 안으로 분배
      - Empty Clause(빈 절): (), 논리적으로 false이자 모순을 의미한다
    - Inference by Resolution
      - KB⊨α 증명
        1. 모순 가정 세팅: 증명을 시작하기 위해, agent의 지식이 참이면서 질의가 false라고 가정한다. 즉, 전체 식을 (KB∧¬α) 로 만든다
        2. CNF 변환: 세팅된 (KB∧¬α) 식 전체를 CNF로 변환
        3. 분해 반복: CNF로 변환된 절들을 살펴, Resolution 규칙을 적용해 상충되는 리터럴을 지우고 New Clause를 계속해서 생성
        4. 모순 확인(빈 절 도출): 과정을 반복하다가 상충되는 단일 명제가 만나 ()이 도출된다면, 처음 세팅한 (KB∧¬α)에서 모순 발생한 것. 따라서 초기 가정이 틀렸으므로, 원래 증명하고자 했던 KB⊨α는 true
        5. 종료: 계속 분해를 진행해도 더 이상 새로운 절이 만들어지지 않고 ()도 나오지 않는다면, KB는 α를 함의하지 않는 것으로 결론 내린다
  - First-Order Logic(1차 논리)
    - vs Propositional Logic(명제 논리)
      - 하나의 사실을 통째로 단일 기호로 만들었던 명제 논리와 달리, 객체와 그들의 속성 및 관계를 분리해서 지식을 훨씬 효율적이고 유연하게 표현하는 논리 체계
      - 1차 논리는 명제 논리보다 표현력이 뛰어나며, 일반적인 법칙을 한 줄의 수식으로 정의할 수 있다
    - Constant Symbol(상수 기호): Ex: Minerva, Pomona, Horace, Gilderoy, Gryffindor, Hufflepuff 등
    - Predicate Symbol(술어 기호): Ex: Person (사람이다), House (기숙사이다), BelongsTo (~에 속한다)
      - Ex: Person(Minerva): 미네르바는 사람이다
      - Ex: BelongsTo(Minerva,Gryffindor): 미네르바는 그리핀도르에 속한다
    - Universal Quantification(∀, 전칭 기호): for all(모든) 객체에 대하여 뒤의 수식이 true임을 나타내는 한정자
      - Ex: ∀x.BelongsTo(x,Gryffindor)→¬BelongsTo(x,Hufflepuff): 모든 객체 x에 대해, 만약 x가 그리핀도르에 속한다면, x는 후플푸프에 속하지 않는다
    - Existential Quantification(∃, 존재 기호): 조건을 만족하는 객체가 적어도 하나 존재한다는 것을 나타내는 한정자
      - Ex: ∃x.House(x)∧BelongsTo(Minerva,x):  x가 기숙사이면서 미네르바가 x에 속하는, 그런 객체 x가 존재한다
