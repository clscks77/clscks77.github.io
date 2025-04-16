---
title: "[Note] 2025-04-15 aml_research_goal.txt"
categories: [Notepad, Daily]
tags: [research, llm]
date: 2025-04-15 21:12:44 +0900
comments: false
--- 
---


- ref: AMLworld 합성 데이터셋 <https://chatgpt.com/c/67e4c065-7510-800b-8809-5ee3ad58c487>

## 좀더 연구 목적과 목표를 확실히 정의하고 들어가야
---
### ■ Problem Define
- 기존의 통계 및 정형 패턴 중심의 탐지는 시간 기반의 전개, 역할 분산, 다중 계좌 등에 대한 탐지가 어려움
    - **단계적 전개, 의도적 분산, 타이밍 조작 등**
      - [x] 얘네 더 구체적으로 조사하기

- 실제 범죄자는 static하지 않기 때문에 훨씬 더 비예측적이고 비합리적인 행동을 보일 수 있음
    - 즉, 통계 및 정형 패턴 중심의 데이터는 **실제 데이터의 subtle한 분포 및 edge case들**을 포착하지 못함
        - **Subtle한 분포**: 기존 탐지 알고리즘이 놓치는 low-signal, high-noise 데이터
        - **Edge case / Novelty**: 전례 없는 돈세탁 전략 (zero-day laundering patterns)
            - 새롭게 등장할 laundering 전략에 대한 generalization 능력 필요
  
- 실제 False Negive 사례를 보면, **시간적인 전략**이 들어갈 경우 탐지를 못하더라
    - *Visual Analysis of Money Laundering in Cryptocurrency Exchange [IEEE Transactions on Computational Social Systems '24]*
        - (이 논문 내용에서 내가 눈여겨 본 것)


> Problem Define은 탐지 쪽으로 설명하는데, 나의 현재 연구 목표는 Data Generation이라서 좀 안맞다고 느끼나?
-  LLM으로 cross-chain 도메인의 자금세탁 탐지를 위한 synthetic 데이터셋을 만드는 연구
-  subtle pattern modeling, 전략적 시뮬레이션, 그리고 미래형 위협 예측

---
### ■ Goal
- LLM의 generative power(statistically novel output)를 **전략성**에 연결하여 시간적으로 구성된 계획, 행동 시퀀스, 상호 연계된 의도를 담아보자
- 이를 위해 해결해야 하는 (새로운 아키텍처를 제시해야 하는) task들
    - [x] 정량화하는 기준
    - [x] 전략적 행동 전개의 정의
    - [x] hallucination을 걸러낼 방법
    - [x] 현실성-전략성 통제
        - [x] 실제 자금세탁 전략의 동기적/합리적 구조 없이 생성한 결과는 plausible하지 않을 수 있음
        - [x] 기술적으로 가능하더라도, 비용·위험·동기 측면에서 실제 범죄자들이 절대 쓰지 않을 전략일 수 있음
        - [x] 결국, **공격자 마인드셋을 시뮬레이션**해서 **plausible한 자금세탁 시나리오**를 생성하는 방향으로 가야 함

- 이후, 텍스트 기반 시나리오에서 GNN 기반 탐지 연구를 위한 거래 그래프 생성

```
- plausible한 시나리오 생성을 위해, LLM으로 공격자 마인드셋을 시뮬레이션하는 방향
```

---
### ■ Insight


---
### ■ Solution




- **plausible하고 diverse한**을 구체적으로 파고 들어가야