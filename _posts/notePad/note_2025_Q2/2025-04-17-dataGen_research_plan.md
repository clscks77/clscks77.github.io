---
title: "[Note] 2025-04-17 dataGen_research_plan.txt"
categories: [Notepad, Daily]
tags: [research, llm, plan. latent_space]
date: 2025-04-17 14:57:24 +0900
comments: false
--- 
---


> Controllable Generation of Strategically Plausible Cross-chain Money Laundering Scenarios via Latent-Guided Large Language Models

```
기존의 통계 및 정형 패턴 중심의 탐지는 시간 기반의 전개, 역할 분산, 다중 계좌 등에 대한 탐지가 어려우며 실제 데이터의 subtle한 분포 및 edge case들을 포착하지 못함. 새롭게 등장할 laundering 전략에 대한 generalization 능력이 필요함. LLM의 generative power를 전략성에 연결하여 시계열적 계획, 행동 시퀀스, 상호 연계된 의도를 담아내는 cross-chain money laundaring 데이터셋(시나리오 기반)을 만들고자 함. (cross-chain에 대한 데이터셋은 전무한 상황)
```

```
탈중앙화 금융에서 크로스체인 자금 세탁은 점점 더 정교한 위협이 되고 있으며, 규칙 기반 또는 정적 통계 모델로는 탐지하기 어려운 복잡한 멀티홉과 시간적으로 구조화된 행동을 수반하는 경우가 많습니다. 이러한 불법 활동에 대한 선제적 탐지와 전략적 모델링에 대한 필요성이 증가함에 따라, 구조화된 잠재 표현을 기반으로 하는 대규모 언어 모델(LLM)을 사용하여 자금 세탁 시나리오를 생성 모델링하는 새로운 프레임워크를 제안합니다. 우리의 접근 방식은 도메인 정보 시나리오 모델링과 반합성 데이터 세트 구성을 결합하고, 생성된 시나리오의 타당성과 다양성 사이의 균형을 제어하기 위해 VAE 기반 잠재 공간을 통합합니다.
저희는 새로 구축된 크로스체인 세탁 행위에 대한 데이터 세트에서 프레임워크를 평가하여 생성된 시나리오의 전략적 현실성과 알려진 패턴을 넘어선 일반화를 모두 측정합니다. 이 연구는 블록체인 생태계에서 선제적인 금융 위협 분석을 위한 LLM 기반 시뮬레이션 도구를 향한 기초적인 단계를 제공합니다.
```

> Cross-chain Money Laundering Scenario Modeling:  
- 크로스체인 자금세탁 행위를 구조화, 시간적 순서, 전략적으로 주도되는 시퀀스로 새롭게 공식화하여 체계적인 분석과 생성을 가능하게 합니다.

> Domain-aware Semi-Synthetic Dataset Construction:  
- 실제 주석이 달린 데이터가 없을 때 도메인 정보 템플릿, 규칙 기반 변형, LLM 지원 증강을 사용하여 고품질 훈련 데이터세트를 구축하는 방법론입니다.

> Latent Space Control of LLM Generation:  
- 생성된 시나리오의 제어된 조작을 통해 타당성과 전략적 참신성 사이의 균형을 맞출 수 있는 잠재 변수 프레임워크(VAE 또는 VQ-VAE 기반)입니다.

> Experimental Evaluation Framework for Scenario Generators:  


---

> '무엇을 생성할 것인가’를 제대로 정의하고, 생성된 시나리오가 실제와 얼마나 유사한지 측정할 기준이 명확해야 함

[1] 현실적으로 plausible하면서도 전략적으로 다양한 money laundering 시나리오를 정의하고, 이를 기반으로 학습용 시나리오 데이터셋을 구성

A. 시나리오 구조 모델링

B. 데이터 수집 및 정제
----> 이것도 LLM으로 해ㄷㄷ



[2] 실험 설계 Mock-up

> 실험 목적: LLM + latent space 기반 시나리오 생성기가 얼마나 현실적인 시나리오를 다양하게 만들 수 있는가?  
- Plausibility 축: 현실적인 전략 시나리오 (domain critic or rule-based validator 통과 가능성)  
- Diversity 축: 시나리오의 구조적 변화량 (actor graph, chain hop, token flow의 다양성)  


1. DiffLM: Controllable Synthetic Data Generation via Diffusion Language Models [ICLR 2025] ★매우 유사
	- VAE + diffusion으로 latent space를 잘 정의하고, 그걸 LLM에 plug-and-play로 주입하여 controllable generation 실현
		- VAE에서 latent space가 LLM에 어떻게 연결되는가
		- diffusion module이 왜 필요한지
2. Uncovering Latent Chain of Thought Vectors in Language Models [ICLR 2025]
	- latent space 조작이 LLM의 행동에 어떻게 영향을 주는지에 대한 중요한 아이디어를 줌
	- LLM의 activation space를 latent vector처럼 조작해서 reasoning style을 바꾸는 방식
		- 이걸 응용해서 "지갑 행동 전략 스타일"을 induction하는 데 쓸 수 있음
			- activation space와 latent vector의 대응
			- vector 삽입 방식: prefix-tuning vs direct attention injection
3. PT-VAE: Variational autoencoder with prior concept transformation [Neurocomputing 2025]
	- 시나리오에 "전략적 의미 축"을 명시적으로 반영하려는 아이디어와 맞음
		- prior concept을 어떻게 정의하고 반영하는가
		- Gumbel-softmax를 활용한 discrete latent space

