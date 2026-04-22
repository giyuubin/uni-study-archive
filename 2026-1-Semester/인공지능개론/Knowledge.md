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

















   
