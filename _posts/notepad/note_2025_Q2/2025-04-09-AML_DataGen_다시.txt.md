---
title: "[Note] 25-04-09 AML_DataGen_다시"
categories: [Notepad, Daily]
tags: [research, aml, synthetic_data, llm]
date: 2025-04-09 21:49:00 +0900
comments: false
excerpt: "자금세탁탐지를 위한 cross-chain 합성 데이터셋을 만들자, 근데 LLM을 쓴"
---
---



- 자금세탁탐지를 위한 cross-chain 합성 데이터셋을 만들자.
    - 최종 데이터 형식은 elliptic을 따르되, cross-chain에 맞게 확장한다.

- 일반 금융 분야의 합성데이터 생성기 AMLworld의 방식과 비교했을 때의 장점
    - AMLworld는 확률기반의 자연스러운 agent행동에 의한 데이터 생성 방식을 사용한다
    - 내가 하고자 하는 것은 특정 조건을 만족하는 시나리오를 생성하고, 이에 대한 Tx 데이터를 생성하는 것.

- 그럼 이렇게 원하는 시나리오의 자금세탁 전략을 현실적이면서 창의적으로 생성하는 것이 어떤 의의가 있느냐?
    > 애초에 비윤리적인 것 아니냐? 세탁을 시도하려는 자들에게 도움을 주는 것 아니냐? ■■■■
    - LLM에게 창의적인 자금세탁 시나리오를 만들어달라고 하면 벤을 당할 것이며, 만약 아니라 하더라도 이걸 벤하는 연구를 진행하는게 더 옳다.
        - 즉, 자금세탁 도메인에서는, 인간 중심의 해석 가능한 시나리오를 포함하는 데이터셋을 만드는 것은 연구하면 안된다?
    > 일부 데이터만 공개하면 어떻게 안될려나,,, 연구용 오픈은 필요하잖아! ◆


- 그럼 다시 내가 당초에 할려던 것으로 돌아와보면, 나는 아무튼 cross-chain을 도메인으로 한 자금세탁 라벨이 있는 데이터셋을 원한다. 그래야 전략 탐지 연구를 하지.
    - 생각할 수 있는 가장 좋은 방법은: AMLworld의 방식처럼 현실적인 확률기반 agent-based 시뮬레이터로 데이터를 생성하고, 확률적으로 가장 좋은 데이터셋을 오픈.
    - 이 시스템을 만드는 데 드는 노력과 시간을 고려하면, 이걸로 논문이 하나 나와야 한다.
    - 그럼 여기서 LLM을 활용하여 AMLworld보다 더 좋은 장점을 가지는 데이터셋을 만들 수 있을까?
    > AMLworld의 한계점은 무엇인가? ■■■■□
        - AMLworld는 철저하게 통계적으로 설계하지만, 실제 인간의 경제적·사회적 행동은 훨씬 더 비예측적이고 비합리적일 수 있음
        - 일부 laundering 전략(예: obfuscation, layering 속도 조절, 랜덤성 등)은 너무 정형화된 형태로 모델링되어 있음  
			즉, 실제 범죄자가 쓰는 복잡하고 비정형적인 행동을 충분히 포착하지 못함
        - 저자들은 약간의 설정 변화로 전체적인 라벨링 비율이나 거래 구조가 급격히 달라질 수 있는 시뮬레이션 안정성 문제가 있다고 언급함  
			(파라미터 민감도 및 안정성 문제)
        - 실제 거래 데이터 없이 통계만으로 모델링하기 때문에, 현실에서만 존재할 수 있는 복잡한 pattern(예: 예외적인 cross-border 거래 흐름 등)은 누락될 수 있음
            - 모든 데이터가 synthetic이라서 실제 데이터의 subtle한 분포나 edge case들을 포함하지 못함
        - 새롭게 등장할 laundering 전략에 대한 generalization 능력이 부족
            - AI 탐지 모델들이 overfitting될 가능성이 높음
        - 기반으로 하는 통계 자체가 미국 금융 환경 중심임
            - 국가별 규제 환경 및 금융 인프라 차이를 충분히 반영하지 못함
    > 요약하면, 철저한 통계 기반(심지어 미국 편향적인)의 시뮬레이션 데이터이기 때문에 창의력이 부족하다 ◆

## 이걸 LLM을 활용해서 해결한다면? ◆◆◆ 
- 일단, 실제 사건 데이터를 기반으로 하면서, 최신 데이터로 지속적으로 업데이트 돼야 함
- 그리고 연구 분야가 LLM based synthetic data generation으로 가게 되는데, 이쪽의 주요 과제는
    - “창의성 ↔ 현실성” 사이의 균형 = Controlled Creativity / Grounded Generative Intelligence
    - 이런 구조/방법은 어떻습니까? 하는 시스템 제안을 해야 하는데,,,,,,,,,,,,
        - 일단, LLM based synthetic data generation 선행 연구 조사가 필요함... 또...
            > 논문리뷰 계획을 짜봅시다. 이번에는 세미나한다는 마인드로 논문정리하기 ■■■■  
            https://github.com/wasiahmad/Awesome-LLM-Synthetic-Data


## 탐지모델까지 같은 논문에서 증명해야 할 것 같다는 생각
> 실험은, 유명한 실제 사건들 탐지해 내는걸로? ◆  
> 내가 만든 합성 데이터로 학습하고, 내가 만든 합성 밴치마크로 테스트하고, 실제 사건 Tx 5개 정도? 넣은 데이터로 테스트 ◆


- 아니면, 데이터 오픈은 안하고, 내 연구에만 써? 나는 자금세탁 전략 탐지하는 연구하고싶은거니까?
    - 일단 데이터셋은 몇개만 오픈하고, 내 전략탐지 GNN을 위한 input module로 쓸까?
    - 그럼 데이터 생성으로 논문을 쓸려면, 이걸 일반화해서, 도메인 특화가 아닌 걸로 해야하나?
        - 일반화 가능할 것 같긴해. “Rag용 외부 데이터 및 파싱 / 지식기반 검증 모듈” 얘네만 좀 어떻게 하면? 애초에 지식기반 검증 모듈이 일반화가 되나…?
            - 그럼 또, 일반적인 LLM based synthetic data generation 연구들과 비교했을 때 어떤 contribution이 있는가? ■■■■
                > 구조적인 차이가 있어야겠지?? 이쪽으로 한번 연구해봐? ◆

    > 그냥, 기존에 있는 LLM기반 데이터 생성기 쓰고, Tx Graph 형태로 변환? 변환모듈만 만들어? ◆
        - 사실 LLM based synthetic data generation 오픈된게 좀 있는 것 같긴해
            > 신뢰성 문제가 있을 수도 있지만? 일단 찾아봐 ■■■■ 



## ◆ 핵심 이슈: Dual Use Dilemma
- AML 탐지를 위해 설계됐지만, 악의적인 사용자는 그걸 이용해 ‘탐지 회피 전략’을 학습할 수도 있음.
    - 일부 예시만 공개?
    - 목적이 탐지강화에 있다는 것을 어필 -> 탐지 모델 성능평가가 꼭 들어가야 

---

Multi-Chain Mixture of Experts

Expert Knowledge 기반 Fine-Tuning
- Supervised Fine-tuning (SFT)
- Instruction Tuning
- Reinforcement Learning with Expert Feedback (RLEF / RLAIF)
- Knowledge Injection / Retrieval-Augmented Fine-tuning
- PEFT(Parameter-Efficient Fine-Tuning)
    - LoRA(Low-Rank Adaptation)
    - Prefix-Tuning / Prompt-Tuning / Adapter Tuning 

- Controlled Creativity / Grounded Generative Intelligence
    - 전문가 모듈 2개 두고, 하나는 창의적으로, 하나는 현실적으로 생각하게 하면 될려나?
    - RLAIF : 다른 LLM이 평가자로 피드백을 주는 방식
    - Multi-Agent Debate + Alignment: 여러 LLM이 서로 다른 관점에서 아이디어를 토론
        ※ “창의성 ↔ 현실성” 사이의 균형은 멀티 스테이지/멀티 에이전트/멀티 신호로 접근

멀리 가는 것 같다...




