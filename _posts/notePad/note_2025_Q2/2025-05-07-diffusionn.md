---
title: "[Note] 2025-05-07 Diffusion & Latent Space"
categories: [Notepad, Daily]
tags: [diffusion, latent_space, study]
date: 2025-05-07 14:16:27 +0900
comments: false
excerpt: "Diffusion 및 Latent Space"
--- 
---


- 함께한 GPT: <https://chatgpt.com/c/681aca14-2a10-800b-8370-98e9defe3be3>

---

## Diffusion 모델 종류 (● 스터디하기)


- Denoising Diffusion Probabilistic Models (DDPM)

- DDIM

- Stable Diffusion

- Latent Diffusion

(텍스트를 위한 Diffusion-like 모델)
- D3PM (Denoising Diffusion for Discrete Data)
- Diffusion-LM


## Auto-regressive 모델에서 Cross-Attention이 사용되는 경우

- 조건부 생성 모델 (Conditional Generative Model)
    - 프롬프트나 외부 정보에 conditioned
    - GPT-2 + Prompt, GPT-3 finetuned for tasks


## Cross-Attention
- 기계 번역 (NMT): Encoder-Decoder Transformer (with cross-attention)
- 텍스트 → 이미지 멀티모달: DALL·E, Stable Diffusion (with cross-attention to text encoder)

- 어떻게 조건을 반영하나?
    - Decoder가 출력을 생성하면서, **조건 정보(Encoder의 출력)**를 cross-attention을 통해 참조함.
        - T5, BART: 텍스트 조건 (encoder) → 텍스트 출력 (decoder)
        > T5, BART가 Cross-Attention을 사용하는 Language Model인가? Diffusion을 사용하는건 아니지?


- 조건을 임베딩하여 decoder 입력에 concat 또는 prepend하는 방식도 사용
    - 예: GPT에서 프롬프트를 통해 조건 부여
    - 예: class label을 vector로 변환하여 GAN에 입력 (e.g., Conditional GAN)

| 모델 이름            | 조건  | 출력  | 구조/기술                                  |
| ---------------- | --- | --- | -------------------------------------- |
| T5               | 문장  | 문장  | Encoder-Decoder Transformer            |
| GPT + Prompt     | 문장  | 문장  | Decoder-only (AR) + 프롬프트 입력            |
| DALL·E           | 텍스트 | 이미지 | Transformer + VQ-VAE                   |
| Stable Diffusion | 텍스트 | 이미지 | U-Net + CLIP Text Encoder + Cross-Attn |
| Conditional GAN  | 클래스 | 이미지 | GAN + 조건 임베딩                           |



## autoregressive 대신 Diffusion을 사용하려는 이유가 뭐지? Diffusion 방식의 장점이 뭐길래? 

(장점/이론적 가능성)
- autoregressive는 병렬화가 불가능하지만, 
    - Diffusion은 전체 문장을 한 번에 샘플링하거나, 전체를 반복적으로 복원하는 구조이기 때문에
    - 전체 문장 생성의 병렬화 가능성이 있음
    - 특히 긴 문장/문서 생성에서 장점
    - 하지만, 이론적으론 병렬화가 가능하지만, 실제 diffusion step 수가 많아지면 오히려 전체 시간은 AR보다 더 큼
        - ◆ 속도와 품질이 개선된 diffusion variant 등장 (예: fewer step inference)


- 모호성/다양성 제어에 유리
    - Diffusion은 분포 자체를 학습하므로, 다양한 출력 샘플을 생성하는 데 강함
    - 특히 **조건부 생성 (조건에 따라 다양한 정답이 있을 수 있는 경우)**에 유리
        - 그럼 GPT는 다양성 확보가 안 되느냐?
            - 현재 GPT도 temperature나 top-k sampling 등을 통해 이미 상당한 다양성 확보 가능
    
- latent space 조작 및 제어 측면에서 Diffusion이 더 직관적이다?
    - 이미지 분야에서처럼, latent space에서 의미 기반 조작이 더 자연스러울 수 있음?
    - 하지만 텍스트 latent space는 훨씬 더 정제되고 민감함 → 약간의 조작으로 문법 오류나 의미 왜곡이 쉽게 발생
    - diffusion이 latent space 제어에 강하다는 가정이 텍스트엔 아직 증명되지 않음

- 글로벌 일관성 확보 가능성
    - AR 모델은 다음 토큰만을 예측하는 방식이므로, 문장 전체 의미 구조를 보장하기 어려움
    - Diffusion은 여러 번 global하게 문장을 "다듬을" 수 있음
        - 하지만, GPT류도 long-range dependency를 잘 학습하고 있음 (특히 GPT-4 같은 대형 모델)



## 생성 다양성과 제어가 중요한 창의적 생성 작업

(● 이 관점에서 AR이랑 DF를 비교하는 것부터 시작)  
(◆그럼, 애초에 실험으로 이 비교를 넣는건? 내가 만든게 ㅁㅁDiffusion이라면 당연히 비교대상으로 GPT류를 넣어야 하나?)

- 요구 조건: 출력 다양성 (diversity), 조건 반영의 정밀도 (controllability), 스타일이나 의미 편집 가능성
- Diffusion은 전체 문장을 하나의 샘플로 전역적으로 생성하니까, 더 다양한 양상을 생성할 가능성이 있다
- Diffusion은 조건 임베딩을 side-channel로 제공 → 더 명시적이고 직관적인 조건 제어 가능할 것이다

- 즉, "다양성과 제어"라는 목적만 놓고 보면, diffusion은 이론적으로 더 유연한 구조를 제공할 수 있음.
    - 그러나 현재 구현 수준에서는 GPT류 autoregressive 모델이 여전히 품질과 실용성 측면에서 강력함
    - 향후 few-step diffusion, guided decoding, editing-friendly latent diffusion 등이 진보되면 경쟁 가능.

- 근데 문제는, discrete 텍스트 공간에서 diffusion은 아키텍처가 복잡함
- 다양성과 제어는 본질적으로 충돌할 수 있음 — diffusion도 완벽한 trade-off를 제공하진 않음
    > ◆ 이 trade-off를 P2P 논문을 기반으로 해결할 수 있지 않을지?



## latent space 제어를 위한 다른 방법에는 어떤게 있니?

- Latent space는 모델이 입력을 압축된 벡터 형태로 표현하는 공간
    - 이 공간에서의 벡터 간 연산이 **의미적인 조작**을 가능하게 만듦


- latent space 제어가 의미하는 바는 뭐지?

| 방식                                                       | 설명                                        | 예시                                         |
| -------------------------------------------------------- | ----------------------------------------- | ------------------------------------------ |
| **Linear Interpolation / Arithmetic**                    | 두 벡터 사이에서 이동하여 변화 유도                      | 감정 변화: 중립 → 긍정                             |
| **Conditional Vectors**                                  | 조건(스타일, 감정 등)을 latent vector로 encode하여 합성 | Style transfer                             |
| **Prompt Engineering (for AR)**                          | 조건을 명시적으로 입력에 반영                          | "in Shakespearean style"                   |
| **Guidance or Classifier-Free Guidance (for diffusion)** | 조건을 따르는 방향으로 샘플 유도                        | Stable Diffusion에서 텍스트 조건 강조               |
| **Latent Code Optimization**                             | 목표 조건에 맞도록 latent를 gradient로 조정           | StyleGAN manipulation                      |
| **Inversion + Editing**                                  | 기존 문장을 latent로 변환 후, 조작하여 다시 복원           | Text inversion → sentiment edit → decoding |


- 하지만, 텍스트에서는 정밀한 조작과 안정성 확보가 어렵다는 단점


- GPT류에서는 latent 접근이 직접적이지 않음 (토큰 시퀀스 기반 생성이기 때문)
    - Diffusion 모델은 latent space를 명시적으로 다루기 때문에, 제어 및 편집 가능성이 열려 있는 구조이나,
    - 아직 그 잠재력을 완전히 실현한 모델은 많지 않음.


## "latent space를 활용한 텍스트 생성의 제어 가능성과 다양성 향상"에 관한 연구


1. Hierarchical Latent Modulation (HLM)
- HLM은 다층(latent hierarchy) 구조를 활용하여 텍스트 생성 시 다양한 속성(예: 감정, 스타일)을 정밀하게 제어할 수 있는 프레임워크입니다. 이 접근 방식은 **저차원 텐서 분해(Low-rank Tensor Factorization)**를 통해 조건부 모듈레이션 파라미터를 생성하며, 이를 통해 생성된 텍스트의 다양성과 일관성을 향상시킵니다. 

2. Latent Diffusion for Language Generation
- 이 연구는 언어 오토인코더의 latent space에서 연속적인 diffusion 모델을 학습하여 자연어 생성을 수행합니다. 이를 통해 조건부 생성, 시퀀스-투-시퀀스 변환 등 다양한 텍스트 생성 작업에서 효율적인 샘플링이 가능함을 보여주었습니다.

3. MacLaSa: Multi-Aspect Controllable Text Generation
- MacLaSa는 다중 속성 제어를 위한 효율적인 샘플링 기법을 제안합니다. 이 접근 방식은 VAE를 기반으로 한 compact latent space를 활용하며, ODE(Ordinary Differential Equations) 기반 샘플러를 통해 다양한 속성을 동시에 제어하면서도 높은 텍스트 품질을 유지합니다. 

4. Gradient-Regularized Latent Space Modulation (GRLSM)
- GRLSM은 **대형 언어 모델(LLM)**에서 구조화된 텍스트 생성을 위해 gradient regularization을 적용한 latent space 제어 기법입니다. 이를 통해 생성된 텍스트의 구조적 일관성과 논리적 흐름을 향상시키며, 다양한 도메인에서의 적용 가능성을 보여주었습니다. 

5. Controllable Text Generation via Probability Density Estimation
- 이 연구는 확률 밀도 추정을 활용하여 latent space에서 텍스트 생성을 제어하는 방법을 제안합니다. Normalizing Flow를 활용하여 복잡한 분포를 단순한 Gaussian 분포로 매핑함으로써, 다양한 속성에 대한 정밀한 제어가 가능함을 입증하였습니다. 

| 연구 주제            | 주요 기법                        | 특징          |
| ---------------- | ---------------------------- | ----------- |
| HLM              | 계층적 모듈레이션                    | 다양한 속성 제어   | 
| Latent Diffusion | 연속 latent space에서의 diffusion | 효율적인 샘플링    |
| MacLaSa          | ODE 기반 샘플링                   | 다중 속성 제어    |
| GRLSM            | gradient regularization      | 구조화된 텍스트 생성 | 
| 확률 밀도 추정         | Normalizing Flow             | 정밀한 속성 제어   | 




