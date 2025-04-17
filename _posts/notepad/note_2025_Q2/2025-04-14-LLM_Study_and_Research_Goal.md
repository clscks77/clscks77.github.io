---
title: "[Note] 2025-04-14 LLM_Study_and_Research_Goal"
categories: [Notepad, Daily]
tags: [research, llm]
date: 2025-04-14 15:16:48 +0900
comments: false
excerpt: "fine-tuning을 어떻게 하면 좋을지부터 고민 시작"
--- 
---


## 일단 지금 걸리는거
---
- [x] 데이터.. few-shot learning이 fine-tuning 단계에 적용되는건줄 알았는데, prompt(추론) 단계만 해당되는거였음
    - few-shot은 모델 파라미터를 아예 안건드리는거

- [x] LoRA, PEFT, prompt-tuning, instruction tuning은 적은 데이터로도 fine-tuning **비슷하게 하는, 가벼운 학습기법**
    - 얘네는 **여전히 모델 파라미터를 일부 조정함**

- [x] **내가 하고싶은 task = 컨텐츠 생성 / 스토리텔링 확장**
    - 그리고 일단 목표는 학습된 데이터에서 더 발전한 창의적인 출력물 생성
        - 근데 이걸 위해 내가 직접 현실에서 일어난 AML 데이터를 10,000개 모은다?
        - 실제 모델 훈련은 훨씬 더 많은 데이터가 필요하니까, 의미있는 짓인가...?

- [x] **사실 그 전에, 꼭 fine-tuning을 해야할까?라는 의문**
    - 기존 LLM (GPT-4 등) 또는 Zephyr/Mistral 등 open model을 쓰고, Few-shot 프롬프트 엔지니어링으로 시나리오 생성하는건?

- [x] **좀더 내가 할려는 연구를 일반화시키고 싶은데**
- [x] **좀더 연구 목적과 목표를 생각해봐야... 탐지까지 가야하나?**
    - (Problem Define) 시간 기반의 전개, 역할 분산, 다중 계좌 등의 탐지는 기존의 정형 패턴 중심의 탐지로는 어려움 (단계적 전개”, “의도적 분산”, “타이밍 조작” 등)
        - 실제 범죄자는 static하지 않기 때문에 문제를 LLM으로 address(?)해야 한다
        - LLM의 generative power를 "전략성"에 연결하여 시간적으로 구성된 계획, 행동의 시퀀스, 상호 연계된 의도를 담아보자
        - "정량화하는 기준", "전략적 행동 전개의 정의", "hallucination을 걸러낼 방법", “현실성-전략성 통제”에 관한 새로운 아키텍처를 제시

```
기존 AML 탐지 시스템은 정형화된 통계적 패턴에 기반하고 있어, 실제 범죄자가 사용하는 창의적이고 시계열적인 자금세탁 전략을 포착하는 데 한계가 있다. 본 연구는 대규모 언어 모델(LLM)의 생성 능력을 활용하여, 시간 기반의 전략적 행동 전개를 포함한 자금세탁 시나리오를 합성하고, 이를 통해 AML 탐지 모델의 robustness와 generalization 능력을 강화하는 새로운 데이터 생성 프레임워크를 제안한다. 생성된 시나리오는 현실성과 전략성을 동시에 확보할 수 있도록 통제되며, 정량적 및 정성적 평가 체계를 통해 그 유효성을 검증한다.

# Generative AML 시뮬레이션  # LLM 기반 금융 범죄 시뮬레이션 아키텍처  # Blockchain  # Cross-chain  # AML
# LLM-Driven Adversarial Simulation Architecture for Cross-chain AML
```

> **"정량화하는 기준", "전략적 행동 전개의 정의", "hallucination을 걸러낼 방법", “통제”에 관한 새로운 아키텍처**
- Multi-agent LLM 시뮬레이션
  - “money mule”, “fraud orchestrator”, “bank compliance AI” 등의 역할을 맡겨서 시나리오의 리얼리즘과 상호작용성 향상
- adversarial fine-tuning (GAN-like) 구조


## PEFT (Parameter-Efficient Fine-Tuning)
---
> 전체 모델 파라미터를 건드리지 않고, 일부 파라미터만 학습해서 빠르고 저렴하게 fine-tuning 하는 기법들
- LoRA (Low-Rank Adapters), Prefix Tuning, Prompt Tuning, Adapters, BitFit, IA3 등


## LoRA (Low-Rank Adapter)
---
- 모델의 원래 weight 행렬을 학습하는 대신, 그 weight에 작은 low-rank 행렬 두 개를 곱하는 방식으로 학습

```
W' = W + ΔW
ΔW = A ## B   (A: rank-r matrix, B: rank-r matrix)  // A, B만 학습 (수천~수만 개의 작은 파라미터)
```

- **QLoRA (LoRA + 4bit 양자화)**
	- **LLaMA 2 13B + QLoRA 방식 + A6000 (1장 이상)** 이렇게 가야 하나


## Prompt-tuning
---
- 모델의 파라미터는 그대로 유지하고
- 입력 프롬프트 앞에 **학습 가능한 가상 프롬프트 벡터(soft prompts)**를 추가
- 이 임베딩 벡터들은 임베딩 공간에 있는 trainable vector들이라서, 프롬프트 자체를 텍스트로 바꾸는 게 아니라 숫자 벡터로 튜닝하는 느낌
- 즉, "어떤 말투로 말하면 얘가 말을 잘 알아듣는지"를 학습하는 것과 비슷함
    - soft prompt는 그 말투를 벡터로 표현한 것
- Soft prompt를 학습하려면, 일반적인 supervised learning처럼 **입력-출력 쌍 (input-output pair)**이 필요  
    ◆ 즉, creativity 측면에서는 도움이 안될 것이다.


## Instruction tuning (SFT 방식)
---
- 다양한 task를 자연어 명령(Instruction)으로 표현한 instruction → response 데이터로 모델을 supervised fine-tuning하는 방식
    - 기존 LLM은 단순한 텍스트 예측(Predict the next word)만 잘하다 보니, “질문에 답해줘”, “이 문장 번역해줘” 같은 명령에 제대로 반응하지 못했음
    - 그래서 사람처럼 **명령을 이해하고 따르는** 능력을 갖추게 하려는 목적으로 instruction tuning이 나옴
        - 명령어가 자연어로 표현되어 있고, 모델이 이를 읽어서 **의미 있는 지시사항을 따르는 법**을 배우는 것

- prompt-tuning과의 차이점 (-의도, 구조, 일반화 목표)
    - Instruction tuning은 모델 전체 또는 일부를 fine-tune함. 그래서 모델의 파라미터 수정이 많음
    - Instruction-tuning이 prompt보다 일반화 성능이 높음 (zero-shot/few-shot에서도 잘 작동)
        - ◆ 다양한 나리오 유형에 대응할 수 있게 할려면 instruction 기반으로 모델을 fine-tuning해야 한다?
            - [x] **다양한 시나리오에 대한 데이터를 수집하긴 할건데, 여기에 국한되지 말고 창의적으로 갔으면 하거든**
                - [x] **여기서 보상 기반의 강화학습을 해줘야 하나? 기존에 없던 출력값에 더 가중치를 주도록?**  
                    ◆ 이쪽을 내가 구조적으로 연구해봐야 하나??

- 의미론적으로 차이점을 정리하면 
    - Prompt-tuning: “이 태스크에서는 이렇게 하면 잘 된다”_를 soft prompt 벡터로 학습
    - Instruction-tuning: “자연어 명령을 보고 어떤 태스크든 처리할 수 있도록” 모델 자체를 훈련


## 창의성 측면
---
- 학습 데이터가 창의적일수록, 모델도 창의적인 패턴을 일반화할 수 있다.
    - 500개의 다양하고 실험적인 시나리오로 학습하면? → 스타일을 배울 수 있다?
        - 즉, 데이터 자체가 창의적이고, 다양성이 충분해야 한다?
- 만약 사전 학습된 LLM이 이미 다양한 구조적 배경지식을 알고 있다면, 적은 예시만으로도 창의적인 답을 만들어낼 수 있을까?
- 모델 크기와 capacity가 충분해야 함
    - 현재 창의성의 기준점은 GPT-3.5 (175B)라고 볼 수 있음
    - LLaMA 2 13B로 괜찮을까? (7B는 작을 것 같다고 함) (13B는 RTX 2080 Ti로는 OOM고, A6000으로 가능)
        - 작은 모델을 대전제로 깔고 연구를 해야겠지?
            - 그럼, Chain-of-Thought prompting, Self-refinement 등의 기법을 추가해야?
            - 이런건 그냥 추가하면 되는거고, 
    - LaMA 2 13B를 instruction tuning 수준으로 튜닝하려면, A6000 2장 필요



