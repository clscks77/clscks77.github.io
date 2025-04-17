---
title: "[Note] 2025-04-15,16 aml_research_goal and plan.txt"
categories: [Notepad, Daily]
tags: [research, llm]
date: 2025-04-15 21:12:44 +0900
comments: false
excerpt: "정확히 내가 어떤 연구를 할려는건지 정리가 안된다,, 정리해보기!"
--- 
---


- ref: AMLworld 합성 데이터셋 <https://chatgpt.com/c/67e4c065-7510-800b-8809-5ee3ad58c487>

## 좀더 연구 목적과 목표를 확실히 정의하고 들어가야
---
### ■ Problem Define
- 기존의 통계 및 정형 패턴 중심의 탐지는 시간 기반의 전개, 역할 분산, 다중 계좌 등에 대한 탐지가 어려움
    - **단계적 전개, 의도적 분산, 타이밍 조작 등**
      - [x] <span style="color:#8e632a">**얘네 더 구체적으로 조사하기**</span>

- 실제 범죄자는 static하지 않기 때문에 훨씬 더 비예측적이고 비합리적인 행동을 보일 수 있음
    - 즉, 통계 및 정형 패턴 중심의 데이터는 **실제 데이터의 subtle한 분포 및 edge case들**을 포착하지 못함
        - **Subtle한 분포**: 기존 탐지 알고리즘이 놓치는 low-signal, high-noise 데이터
        - **Edge case / Novelty**: 전례 없는 돈세탁 전략 (zero-day laundering patterns)
            - 새롭게 등장할 laundering 전략에 대한 generalization 능력 필요
  
- 실제 False Negive 사례를 보면, **시간적인 전략**이 들어갈 경우 탐지를 못하더라
    - *Visual Analysis of Money Laundering in Cryptocurrency Exchange [IEEE Trans. on Computational Social Systems '24]*
        - (이 논문 내용에서 내가 눈여겨 본 것)


> Problem Define은 탐지 쪽으로 설명하는데, 나의 현재 연구 목표는 Data Generation이라서 좀 안맞다고 느끼나?
-  **LLM으로 cross-chain 도메인의 자금세탁 탐지를 위한 synthetic 데이터셋을 만드는 연구**
-  **subtle pattern modeling, 전략적 시뮬레이션, 그리고 미래형 위협 예측**

---
### ■ Goal
- LLM의 generative power (=statistically novel output)를 **전략성**에 연결하여 <span style="color:#8e632a">**{Prblem Define} 시계열적 계획, 행동 시퀀스, 상호 연계된 의도**</span>를 담아보자
- **이를 위해 해결해야 하는 (새로운 아키텍처를 제시해야 하는) task들**
    - [x] 정량화하는 기준
    - [x] 전략적 행동 전개의 정의
    - [x] hallucination을 걸러낼 방법
    - [x] 현실성-전략성 통제
        - [x] 실제 자금세탁 전략의 동기적/합리적 구조 없이 생성한 결과는 plausible하지 않을 수 있음
        - [x] 기술적으로 가능하더라도, 비용·위험·동기 측면에서 실제 범죄자들이 절대 쓰지 않을 전략일 수 있음
        - [x] 결국, **공격자 마인드셋을 시뮬레이션**해서 **plausible한 자금세탁 시나리오**를 생성하는 방향으로 가야 함

- 이후, 텍스트 기반 시나리오에서 GNN 기반 탐지 연구를 위한 거래 그래프 생성

<div class="tip-box tip-yellow">
    <i class="fas fa-lightbulb tip-icon"></i>
    ◆ Goal: plausible한 시나리오 생성을 위해, LLM으로 공격자 마인드셋을 시뮬레이션하는 방향 <br>
    ◆ How:  다양하면서도 현실성 있는 시나리오를 어떻게 생성할 것인가
</div>

1. Domain knowledge 반영 (→ Domain Adaptation)
   - 이게 fine-tuning이 돼야겠지?
2. plausible ↔ diverse 밸런스 조절 (★novelity, contribution이 나와야 하는 파트)
   - 다양하면서도 현실성 있는 시나리오를 어떻게 생성할 것인가?
     - **plausible하고 diverse한**을 구체적으로 파고 들어가야 함
     - 전략 유형별로 diversity control parameter 도입
       - Novelty score 산출 (기존 전략과의 cosine similarity 등)
3. 좋은 synthetic laundering 시나리오에 대한 평가기준 정의
   - 고민...
4. 실제 laundering 전략에 대한 ground truth 데이터 수집
    - 과거 OFAC 제재 사례, 사법기관 보고서 등에서 전략 구조 수작업 추출


---
### ■ Insight (by GPT): Controllable Generation
> **시나리오 생성의 latent space를 정의하고 parameter를 조절하여 Controllable Generation 해보자**

<span style="background-color:#e9d5b8">**1. 전략 잠재 공간 (Strategy Latent Space)**</span>
   - 전략을 구성하는 요소(믹서 사용 여부, 체인 수, 시간 간격, fee 구조 등)를 latent vector로 표현하고,  
   **이 공간을 조작하여 다양성과 현실성을 제어해보기?**
     - LLM이 생성한 시나리오 → Vector representation (e.g., BERT embedding + clustering)
     - 이 latent space를 조작하여:
       1. realisticity constraint (known pattern과 cosine similarity 유지)
       2. diversity constraint (clustering distance 조절)
     - **Variational AutoEncoder (VAE)**나 **Contrastive Learning**을 활용해 전략 유형 간 거리를 학습
  
```python
"Strategy Latent Space": 자금세탁 전략 시나리오들을 '의미론적으로 요약된 벡터 공간'에 투영하여, '전략 간 유사도 파악' 및 '다양성 조절'에 대한 탐색/조작을 수행하는 의미 기반 생성 컨트롤 구조

"의미론적으로 요약된 벡터 공간": 

"전략 간 유사도 파악 및 다양성 조절": 거리 계산 (cosine similarity, Mahalanobis, etc.) → novelty 측정

"ㅁㅁㅁ에 전략 구성 요소별 parameter 설정": 이거까지 하던가 말던가
    - latent space를 parameter로 제약
    - VAE나 β-VAE 계열 사용하여 각 dimension이 특정 파라미터와 대응되도록 학습
```

<span style="background-color:#e9d5b8">**2. Novelty Regularizer**</span>
   - Novelty score = embedding similarity to known 전략 시나리오
   - tuning loss += λ * (1 - novelty score)

<div class="tip-box tip-blue">
    <i class="fas fa-lightbulb tip-icon"></i>
    아직 잘 모르겠지만, parameter 관점에서 위에꺼랑 엮어서 Controllable Generation 할 수 있지 않을지,,,
</div>


---
### ■ Solution


---
