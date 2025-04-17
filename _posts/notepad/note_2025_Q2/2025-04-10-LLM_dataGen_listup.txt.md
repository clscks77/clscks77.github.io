---
title: "[Note] 25-04-10 LLM_dataGen_listup"
categories: [Notepad, Daily]
tags: 
date: 2025-04-10 21:49:00 +0900
comments: false
excerpt: "Awesome-LLM-Synthetic-Data 논문들 훑어보기"
---
---

<div class="tip-box tip-blue">
	<i class="fas fa-lightbulb tip-icon"></i>
	내가 이쪽 공부하는게 맞나,,,, 그냥 오픈소스 프레임워크 사용해서 데이터 만드는게...? 그걸 하기위한 공부인가? 흠
</div>

> **통계 기반, 패턴 기반에서 벗어나서 실제 범죄자가 쓰는 복잡하고 비정형적인 행동을 충분히 반영할 수 있는 데이터 및 탐지 모델을 만드는게 목표**  → **프레임워크 만들고, 데이터셋 생성해서 일부 공개하고, 이걸로 기존의 GNN 탐지 모델들이 어떻게 탐지하는가 평가?**
- 도메인 특화 프레임워크/시스템 디자인  
- Synthetic data의 실증적 효과 입증  
- 3-Stage Encoder-Decoder Pipeline ? 효과 분석 ??? 암튼 효과 분석이 가능한 실험 설계 필요  


- ref: Awesome-LLM-Synthetic-Data <https://github.com/wasiahmad/Awesome-LLM-Synthetic-Data?tab=readme-ov-file#310-factuality>

## ■ Surveys
---
### ○ On LLMs-Driven Synthetic Data Generation, Curation, and Evaluation: A Survey [ACL Findings 2024]
<https://cobusgreyling.medium.com/llm-driven-synthetic-data-generation-curation-evaluation-33731e33b525>

### ○ Generative AI for Synthetic Data Generation: Methods, Challenges and the Future [arXiv 2024]



## ■ Alignment & Reward Modeling
---

- Alignment : LLM이 사람의 의도, 선호에 맞게 행동하고 응답하도록 만드는 것
  
### ○ SALMON: Self-Alignment with Instructable Reward Models [ICLR 2024]
- RLHF+SFT 방식은 사람의 폼이 많이 듦. instructable reward model로 인간이 정의한 소수의 원칙(principles)만을 사용하면서도 뛰어난 성능을 달성할 수 있음


### ○ West-of-N: Synthetic Preferences for Self-Improving Reward Models

  
  

## ■ Instruction tuning
---
### ○ Synthesizing Text-to-SQL Data from Weak and Strong LLMs [ACL 2024]
	- 오픈소스 LLM을 "Text-to-SQL" 작업에 특화되게 fine-tuning하는 새로운 방법을 제안
	- preference learning → Instruction tuning
		*Instruction tuning: 모델이 "사람이 내린 명령(instruction)"을 더 잘 이해하고 수행하게 만드는 fine-tuning의 한 종류 (Alignment 하위개념)
---> Structured Data 변환 파이프라인= LLM을 이용한 structured output generation 기법을 활용 가능
	- GraphGen, LLMs as Graph Builders

### ○ CodecLM: Aligning Language Models with Tailored Synthetic Data [NAACL 2024] ◆◆
	- Encode-Decode + Contrastive Filtering을 LLM에 적용한 instruction tuning
	- LLM을 '코덱(codec)'처럼 활용해서, 더 목적에 맞고 데이터 효율적인 instruction-following 학습 데이터를 생성하는 프레임워크


### ○ Learning to Generate Instruction Tuning Datasets for Zero-Shot Task Adaptation [ACL Findings 2024] ◆◆Bonito
	- annotation(지시문-응답 쌍)이 없는 일반 텍스트로부터 자동으로 (instruction tuning에 사용할 수 있는 학습 데이터를 생성해주는 모델
	- 이 synthetic 데이터로 기존 LLM을 다시 instruction tuning 하면 새로운 도메인에서도 zero-shot 성능이 크게 향상됨
		- LM을 특정 업무나 도메인에 쉽게 적응시킬 수 있게 해주는 오픈소스 시스템


Self-Instruct / Alpaca / Evol-Instruct 계열??  
  

## ■ 사실성(Factuality)
---
### ○ MiniCheck: Efficient Fact-Checking of LLMs on Grounding Documents [EMNLP 2024]
	- GPT-4 수준의 성능을 제공하면서도 400배 더 저렴한 비용으로 소규모 사실 확인 모델을 구축하는 방법  


  

## ■ Adversarial Attack
---
### ○ Rainbow Teaming: Open-Ended Generation of Diverse Adversarial Prompts [NeurIPS 2024]
	- 적대적 프롬프트 생성을 품질-다양성 문제로 간주


-----------------------------------------

> Direct Preference Optimization (DPO)
> Domain-specific DSL



-----------------------------------------
- Towards an AI Co-Scientist [arXiv 2025] *AI Co-Scientist
- 멀티 에이전트 시스템
- 생성-토론-진화(generate, debate, evolve)
- 비동기적(asynchronous) 작업 실행 프레임워크를 사용하여 대규모 계산 자원을 유연하게 활용할 수 있다는 점
