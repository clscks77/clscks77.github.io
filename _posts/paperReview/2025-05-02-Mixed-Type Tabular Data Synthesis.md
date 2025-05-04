---
title: "[Gen] Mixed-Type Tabular Data Synthesis with Score-based Diffusion in Latent Space [ICLR 2024]"
categories: [PaperReview, GenerativeAI]
tags: [latent, diffussion, synthetic_data]
date: 2025-05-02 19:03:21 +0900
comments: false
excerpt: "VAE + Diff 데이터 생성 관련 논문 리뷰: Mixed-Type Tabular Data Synthesis"
--- 
---




- 다양한 데이터 타입(연속형, 범주형 등)이 혼합된 고품질로 표 형식(tabular)의 데이터를 생성하기 위한 확률 생성 모델을 제안

- diffusion model의 한계
    - 원래 연속적인 이미지 데이터 생성에 적합한 Diffusion Model을 범주형 등의 다양한 혼합 데이터에 맞춰 설계


- 제안 기법: TABSYN
    - VAE를 이용한 latent space 구성
    - Transformer 구조를 활용해 열 간 관계(inter-column dependency)까지 학습
    - Diffusion 모델을 이용하여, latent space에서 Gaussian 노이즈를 추가해 확산
    - 역확산(reverse process)을 통해 새로운 임베딩 샘플 생성 후 디코딩


- 주요 구성 요소
    - Encoder & Decoder: Transformer 기반 → 각 열의 특성과 열 간 관계를 잘 학습 가능
    - Token-level representation: 열별로 토큰 수준의 임베딩을 생성
    - Adaptive Loss Weighting: 재구성 손실(reconstruction loss)과 KL-divergence 손실 사이의 가중치를 동적으로 조정
    - Score-Based Diffusion in Latent Space: Latent Diffusion Model (LDM) 방식에 linear noise schedule 사용

---

### 3.1 PROBLEM DEFINITION OF TABULAR DATA GENERATION

3.1절에서는 먼저 표 형식 데이터 생성 작업을 공식적으로 정의

- 입력: 혼합 타입(mixed-type)의 표 형식 데이터셋
    - 서로 다른 의미와 타입을 갖는 열들의 집합

- 표형식 데이터의 특징: 표 형식 데이터는 서로 다른 열이 뚜렷한 의미를 가지며 서로에 대한 의존성이 높은 혼합형 열 특징으로 고도로 구조화되어 있습니다. 
    - 이러한 특성으로 인해 열 간의 풍부한 관계를 모델링하고 효과적으로 활용하기 위한 근사 인코더를 설계하기가 어렵습니다. 
    -  표 형식 데이터의 분류/회귀에서 Transformer가 성공한 사례(Gorishniy et al., 2021)
    -  먼저 각 열에 대해 고유한 토큰화기를 학습한 다음, 열 간의 복잡한 관계를 포착하기 위해 토큰(열) 단위의 표현을 Transformer에 입력

- 조건부 생성이 아닌, 전체 분포 pθ(T)를 모델링하는 것이 목표 (현실적이고 다양한 합성 데이터 생성)


```
> 표 형식 데이터만의 도전과제(어려운 이유)가 무엇인지? 본 논문에서는 그걸 어떻게 해결했는지?
> 다층 Transformer를 사용해 열 간 관계를 캡처?
> Adaptive β Scheduling을 이 논문에서 만들었구나. 왜 Adaptive하게 해야 한다고? Ablation이 궁금한데, 역시 뒤에 있네. 
> 실험에 쓰인 matrix는 이 논문에서 개발한건가?

> DiffLM에서 쓰는 Transformer LM encoder도 TabSyn 인코더랑 같은건가?

> 왜 Diffusion을 사용했을까? 왜 VAE를 사용해야 할까?
> related works에서 이렇게 결합한 연구는 없었나?
> 왜 tabular data에서는 기존 확산 모델처럼 매우 많은 step을 거치는 것이 비현실적이지?
> Linear Noise Schedule을 이 논문에서 제안한거야? 아니면 다른 논문의 내용을 가져온거야?

```

---

### 3.2 AUTOENCODING FOR TABULAR DATA

- Feature Tokenizer (열 단위 임베딩 생성)
    - 수치형(numerical) 컬럼의 경우, 선형 변환을 통해 d-차원 벡터로 변환
    - 범주형(categorical) 컬럼의 경우, 원-핫 인코딩 후 임베딩 테이블을 통해 임베딩 벡터 생성
    - 최종적으로 하나의 레코드를 하나의 임베딩 행렬로 표현

- Transformer Encoding and Decoding.
    - 다층 Transformer를 Encoder로 사용해 각 열 임베딩 간의 관계(상관성, 상호의존성)를 모델링
    - Decoder: 잠재 벡터 z → 다시 토큰 임베딩 행렬로 변환

- Detokenizer.


- Training with adaptive weight coefficient.




### 3.3 SCORE-BASED GENERATIVE MODELING IN THE LATENT SPACE


----

## 2 RELATED WORKS

### Generative Modeling in the Latent Space. 

While generative models in the data space have achieved significant success, latent generative models have demonstrated several advantages, includ- ing more compact and disentangled representations, robustness to noise, and greater flexibility in controlling generated styles (van den Oord et al., 2017; Razavi et al., 2019; Esser et al., 2021). For example, the recent GAN literature (Li et al., 2022) has demonstrated superior controllability via adversarial learning in the latent space. Recently, the Latent Diffusion Models (LDM) (Rombach et al., 2022; Vahdat et al., 2021) have achieved great success in image generation as they exhibit better scaling properties and expressivity than the vanilla diffusion models in the data space (Ho et al., 2020; Song et al., 2021b; Karras et al., 2022). The success of LDMs in image generation has also inspired their applications in video (Blattmann et al., 2023) and audio data (Liu et al., 2023a). To the best of our knowledge, the proposed work is the first to explore the application of latent diffusion models for general tabular data generation tasks.

데이터 공간에서의 생성 모델은 상당한 성공을 거두었지만, 잠재적 생성 모델은 더 간결하고 얽히지 않는 표현, 노이즈에 대한 견고성, 생성 스타일 제어의 유연성 등 몇 가지 장점을 입증했습니다(van den Oord 외., 2017; Razavi 외., 2019; Esser 외., 2021). 예를 들어, 최근의 GAN 문헌(Li et al., 2022)은 잠재 공간에서 적대적 학습을 통해 우수한 제어 가능성을 입증했습니다. 최근에는 데이터 공간에서 바닐라 확산 모델보다 더 나은 스케일링 특성과 표현력을 보이는 잠재 확산 모델(LDM)(Rombach et al., 2022; Vahdat et al., 2021)이 이미지 생성에서 큰 성공을 거두었습니다(Ho et al., 2020; Song et al., 2021b; Karras et al., 2022). 이미지 생성에서 LDM의 성공은 비디오(Blattmann 외., 2023) 및 오디오 데이터(Liu 외., 2023a)에도 적용될 수 있도록 영감을 주었습니다. 우리가 아는 한, 제안된 연구는 일반적인 표 형식 데이터 생성 작업에 잠재 확산 모델을 적용한 최초의 연구입니다.


Latent Diffusion Models(LDM) [ICCV, 2022]

