---
title: "[Note] 2025-04-16 Latent Space.txt"
categories: [Notepad, Daily]
tags: [research, study, generative_ai, latent_space]
date: 2025-04-16 21:03:40 +0900
comments: false
excerpt: "잠재 공간(Latent Space)에 대한 공부(intro)"
--- 
---

## ■ Latent Vector
- 입력 데이터를 압축하거나 추상화한, 의미 있는 저차원 공간
	- Embedding 같은건가? SNE(Stochastic Neighbor Embedding)랑 비슷한건가?


## ■ Embedding
- Embedding은 주로 텍스트 데이터에 사용되며, 특정 데이터를 벡터로 변환하는 방법이고, 
- Latent Vector은 데이터의 내제된 정보를 표현하는 압축된 벡터로 텍스트, 이미지, 영상 등 모든 종류의 데이터에 적용될 수 있음


## ■ Autoencoders
- Autoencoders라는 신경망을 이용해서 784 차원 이미지 데이터를 32차원 Latent Vector로 압축
- Autoencoders는 입력 데이터를 압축해서 중간층 (bottleneck layer)에서 latent representation을 생성하는 구조. 
	- 여기서의 중간 표현이 현대적으로 말하는 latent space에 가장 가까움.


## ■ Variational Autoencoder (VAE, Kingma & Welling, 2013):
- "확률적 latent space"를 명시적으로 모델링함
	- [x] 결국	샘플링을 통해 다양한 출력을 만들어내는 VAE가 Latent Space의 종착점인가?
		- GAN > VAE > Transformer > LLM 인줄 알았는데, LLM + VAE(latent space)쪽 연구도 진행되고 있다네?

> **GAN → VAE → Transformer → LLM 흐름인 줄 알았는데?**
- VAE가 LLM에 다시 적용되는 연구가?
	- 기존의 LLM은 latent space가 명시적이지 않아서 조작 및 해석이 어렵다. 
	- 그래서 **제어 가능한 latent space**가 필요하다는 인식이 커졌고, 그걸 잘하는 모델이 바로 VAE인 것!
- **즉, LLM의 한계(제어력 부족, 구조적 표현 부족)를 VAE로 보완하려는 연구가 활발해졌다!**
	- 진짜,,, 재밌긴한데,,,,, 너무 빠르다 이 분야,,, 천재가 많은가봐,,,,,


## 관련 논문 리스트
---
> LLM과 VAE를 통합하거나, latent space를 조작하여 출력(생성)을 제어하는 방법에 대한 연구들

1. **Uncovering Latent Chain of Thought Vectors in Language Models [ICLR 2025]**
	- 언어 모델의 추론 과정을 "Steering Vectors"라는 latent vector를 통해 제어하는 방법을 제안

2. **Mixed-Type Tabular Data Synthesis with Score-based Diffusion in Latent Space [ICLR 2024] ★상당히 마음에 드는 논문**
	- 변이 자동 인코더(VAE)로 제작된 잠재 공간 내에서 확산 모델을 활용하여 테이블 형식 데이터를 합성하는 방법론
		- (1) 일반성: 다양한 데이터 유형을 단일 통합 공간으로 변환하여 처리하고 열 간 관계를 명확하게 포착할 수 있는 능력; 
		- (2) 품질: 잠재 임베딩의 분포를 최적화하여 확산 모델의 후속 학습을 향상시켜 고품질 합성 데이터 생성에 도움을 줌; 
		- (3) 속도: 기존 확산 기반 방법보다 역방향 단계가 훨씬 적고 합성 속도가 빠름.

3. **DiffLM: Controllable Synthetic Data Generation via Diffusion Language Models [ICLR 2025] ★오 내가 지금 하려던거랑 비슷**
	- 오픈 리뷰 재밌네 <https://openreview.net/forum?id=fRmfDqZ2yq&utm_source=chatgpt.com>
	- VAE를 통해 실제 데이터의 분포를 latent space에 매핑하고, diffusion 과정을 통해 이 latent space를 정제합니다.
	- 이후, 정제된 latent vector를 LLM의 디코딩 과정에 soft prompting 방식으로 주입하여, 고품질의 제어 가능한 synthetic 데이터를 생성

4. **PT-VAE: Variational autoencoder with prior concept transformation [Neurocomputing 2025]**
	- 모델 내 정보 수집 과정을 명확하게 설명하고, 얽혀 있지 않은 표현들을 밝혀내는 해석 가능한 잠재 공간 제시


> 일단 이 정도만 찾고, 읽어보자.  
	구조 설계를 어떻게 할지 생각하면서


<div class="tip-box tip-blue">
	<i class="fas fa-lightbulb tip-icon"></i>
  	KL-vanishing 문제? <br>
	- Optimus, LlaMaVAE 같은 논문들은 이걸 해결하기 위해 여러 트릭을 씀 (free-bits, skip connection 등)<br>
	- 즉, "VAE를 쓰는 게 맞을까?"보다는 → "어떤 형태의 latent model이 이 목적에 적합할까?"가 더 중요한 질문
</div>






