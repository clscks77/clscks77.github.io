---
title: "[Note] 25-04-07 AML_LLM_relatedwork"
categories: [Notepad, Daily]
tags: 
date: 2025-04-07 21:49:00 +0900
comments: false
---
---

## 개인연구 TODO

나는 지금 Cross-chain 환경에서의 AML 연구를 위해서 먼저 데이터 생성 연구를 하고 있어. 현재 일반 금융 도메인에서의 가장 좋은 데이터 생성기는 Realistic Synthetic Financial Transactions for Anti-Money Laundering Models [NIPS ‘24] 논문에서 제안하는 AMLworld야. agent-based simulator로 현실적인 AML 데이터를 생성해냈어. 나는 AMLworld를 그대로 블록체인 도메인으로 변환하기 보다는 좀더 연구적인 contribution이 있는 새로운 idea를 찾고 있어. 지금 내 idea는 다음과 같아. prompt와 LLM을 활용해서 현실성 있고 합리적인 자금 세탁 시나리오를 생성하는 데이터 생성기를 만드는 거야. prompt를 통해 원하는 결과를 만들면서, LLM을 agent로 활용해서 그 속에서 창의력을 발휘하는거야. 단, 현실성이 있어야 하기 때문에 도메인 지식 기반 규칙 필터링 모듈을 뒤에 붙여서 feedback을 활용한 LLM fine-tuning을 할 생각이야. 내 idea가 연구적으로 어떤지 전문가적인 관점에서 비판적으로 평가해줘.
----- ◆ LLM 기반 시나리오 생성 선행연구 (LLM 기반 시뮬레이션 연구는 기존에 없었나? 있다면 어떻게 연구 중인지?)
----- ◆ LLM 연구부터 슥 살펴봐야 하나...?? 현재 LLM 수준을 잘 모르겠네... Llama 쓰면 다 되지않을지^-^


나는 지금 Cross-chain 환경에서의 AML 연구를 위해서 먼저 합성 데이터 생성 연구를 하고 있어. LLM을 활용해서 합리적이고 창의적인 자금 세탁 시나리오를 만들고, 이를 Transaction Data로 바꾸고 싶어.

##1단계: RAG 기반 도메인 정보 Retriever (Cross-chain 트랜잭션의 기초 이해 확보) 
- 문서 구성 전략 연구 : Retrieval knowledge 내용 및 구조를 어떻게 해주는게 좋을지? 
- Retriever tuning 연구 : Chunk size, embedding model 등 조정하기 


##2단계: 창의적인 시나리오 생성 (Creativity 높이기) 
- Prompt Engineering 연구
-> Prompt 설계만으로도 창의성 크게 향상 가능 (e.g., “돈세탁범처럼 상상해봐”) 
-> Few-shot?이 이런 내용인가? 
- multi-step prompting으로 서사적 시나리오 생성하기? 


##3단계: 도메인 기반 필터링 모듈 설계 (판단 기준 및 평가 모델 정형화하기) 
- “이 시나리오가 plausible한가?”를 자동 평가할 수 있을까? 


##4단계: Creativity vs. Realism 균형 조정 (Feedback Learning)
- Lightweight reward model 활용 
- LoRA + filter feedback 방식의 domain-aligned tuning 


##5단계: 시나리오 → 구조화된 Tx 데이터
- 텍스트 기반 시나리오를 어떻게 cross-chain graph로 전환할까? 
- 특히 cross-chain 이동, mixing, swap 등을 그래프적으로 구성 
-> Entity/Event 추출 → Tx graph 구성 

각 단계별 목표와 연구 포인트를 다시 정리해봤어. 


■ Related Work 찾아보기
- (데이터 생성 논문) NIPS Datasets and Benchmarks (D&B) 트랙에 또 어떤 논문들이 게재됐는지?
- "데이터 생성 관련" LLM 연구 리스트 뽑아보기
- prompt 기반 AML 어쩌구, 이런 논문 있는지
------ 여기서 더 발전할만한 구석 찾으면서 리뷰하기



■ Background
- Agent-Based Simulation
	- AMLworld 논문 Section 3.4, 3.5 내용
	- Agent-Based Modeling of Social Systems
- trial-error 등 agent의 기술적 차이 공부	// 이건 다른 분야이긴한데,, 도움이 될지도?



■ 마침, 비전 발표 주제가 Text-guided image editing인데, 연관 있어보이잖아? --- 슥 봐봐
- Prompt-to-Prompt Image Editing with Cross Attention Control, ICLR'23
- Zero-shot Image-to-Image Translation, SIGGRAPH'23
- InstructPix2Pix: Learning to Follow Image Editing Instructions, CVPR'23
- Plug-and-Play Diffusion Features for Text-Driven Image-to-Image translation, CVPR'23



■ ML 서비스들 동작 분석 	// AMLworld처럼 갈려면 뭐 찾아야 되노
- ★★★이게 중요하긴 해★★★


자연어 생성(NLG)

Implicit Models - GAN

Explicit Latent Variable Models - VAE

Likelihood-based Models - Autoregressive Models (PixelCNN 등)

Score-based Models - Diffusion Model (DDPM, Stable Diffusion 등)

Transformer-based Models - (Autoregressive Transformer, Image GPT, DALL·E 등)

Retrieval-Augmented Generation (RAG for Generative Models)


1. "AMLworld는 future-looking 시뮬레이션 능력이 제한적임" -> 그럼 이 연구의 결과물은 창의적이야? Prompt를 넣어주는건 사람인데? 그럼 사람이 창의성을 발휘해야 하는거잖아? 사람이 창의적이지 않은 일반적인 prompt를 줘도 LLM based agent가 창의적인 전략을 만든다는거야?
2. "새로운 툴이 등장해도 프롬프트만 바꾸면 대응 가능" -> 이거 맞아? 새로운 툴의 특징과 행동을 새로 학습해야 하지 않을까?
3. "LLM을 탐지 모델이 아니라 행위 주체(agent)로 활용하는 첫 사례" -> 이런 연구가 기존에는 없었다고? 있었을 것 같은데?


============================================================================================
- Diffusion이 먼데
- 차이점, contribution이 뭔지 잘 모르겠네

============================================================================================

● Prompt-to-Prompt Image Editing with Cross Attention Control, ICLR'23 (22.08)
---- 여기 abstract 적은거 내 연구랑 궤가 비슷하다
---- 여기 리뷰 좋네 https://jang-inspiration.com/prompt-to-prompt-image-editing-with-cross-attention-control

Abstract
- 22년도 기준, text-prompt를 통한 이미지 생성 AI가 좋은 성능을 보임 ◆ 관련 연구 체크
- 이를 확장한 text-prompt 기반 이미지 편집 기술을 연구함
- 이 연구의 crux는 ...
- 현재 SOTA의 해결법은 ... 사용자가 편집을 지역화하기 위해 공간 마스크를 제공하도록 요구하여 마스크된 영역 내의 원래 구조와 내용을 무시함으로써 이를 완화
- 이 논문에서는 ... 마스크 같은거 안쓰고, 오직 prompt만 사용하도록 해보겠다! ◆ 왜? 기존 방법 대비 이 방법의 이점이 뭐임?
	- 직관적인 프롬프트 간 편집 프레임워크



● InstructPix2Pix: Learning to Follow Image Editing Instructions, CVPR'23 (23.01) (22.11)
---- [17] Prompt-to-Prompt ---> Generative models for image editing // 이것도 비교 많이 하는데?

Abstract
- 훈련 데이터: 사전 훈련된 "대규모 모델(GPT-3) & 텍스트-이미지 모델(Stable Diffusion))"의 지식을 결합하여 대규모 이미지 편집 예제 데이터 세트를 생성함
- 조건부 확산 모델인 InstructPix2Pix
	- 생성된 데이터에서 training되고 inference 시간에 실제 이미지와 사용자가 작성한 지침으로 일반화됨
	- forward pass에서 편집을 수행하고 example별 fine-tuning이나 inversion이 필요하지 않으므로, 몇 초 만에 이미지를 빠르게 편집할 수 있음



● Plug-and-Play Diffusion Features for Text-Driven Image-to-Image translation, CVPR'23 (23.06)  (22.11)
---- Prompt-to-Prompt (P2P) [17]
---- Plug and Play Language Models (PPLM) 인가 이게?

Abstract
- 핵심적인 과제는 사용자에게 생성된 콘텐츠에 대한 제어권을 제공하는 것이다
- 이 논문에서는 텍스트-이미지 합성을 이미지-이미지 변환의 영역으로 끌어올리는 새로운 프레임워크를 제시
- 안내 이미지와 대상 텍스트 프롬프트를 입력으로 제공하면, 
- 사전 훈련된 텍스트-이미지 확산 모델의 힘을 활용하여 안내 이미지의 의미적 레이아웃을 유지하면서 대상 텍스트를 준수하는 새로운 이미지를 생성
- 우리는 모델 내에서 공간적 특징과 자체 주의를 조작하여 생성된 구조에 대한 세밀한 제어를 달성할 수 있음을 관찰함
- 안내 이미지에서 추출한 특징을 변환된 이미지의 생성 프로세스에 직접 주입하여 훈련이나 미세 조정이 필요 없는 간단하고 효과적인 접근 방식이 제공



● Zero-shot Image-to-Image Translation, SIGGRAPH'23 (23.07) (23.02) // 이 중엔 제일 최신인데, 이후로 더 나왔겠지?
---- Prompt-to-Prompt [Hertz et al. 2022] 많이 언급하네 
---- InstructPix2Pix [Brooks et al. 2022] 이것도
---- Plug&Play [Tumanyan et al. 2023] 이건 한 줄

Abstract
- 현존하는 텍스트-이미지 생성 모델들을 실제 이미지 편집에 적용하는 것이 어려운 이유 2가지
	1) 사용자가 입력 이미지의 모든 시각적 세부 사항을 묘사하는 완벽한 텍스트 프롬프트를 만들어야 함 (=prompt의 어려움)
	2) 종종 인풋을 극단적으로 변형하고 원치 않는 영역에 예상치 못한 변화를 도입해버림 (=급발진)
- 수동 프롬프트 없이 원본 이미지의 콘텐츠를 보존할 수 있는 이미지-이미지 변환 방법인 pix2pix-zero를 소개하겠다
	◆ ??? 아직 무슨 말인지 모르겠네
	- 먼저 텍스트 임베딩 공간에서 원하는 편집을 반영하는 편집 방향을 자동으로 발견
	- 콘텐츠 구조를 보존하기 위해 확산 프로세스 전반에 걸쳐 입력 이미지의 교차 어텐션 맵을 유지하는 것을 목표로 하는 교차 어텐션 가이드를 제안
	- 마지막으로 대화형 편집을 가능하게 하기 위해 확산 모델을 빠른 조건부 GAN으로 정제
	- 추가적인 훈련이 필요하지 않으며, 기존의 사전 훈련된 텍스트-이미지 확산 모델을 바로 사용할 수 있습니다. 























