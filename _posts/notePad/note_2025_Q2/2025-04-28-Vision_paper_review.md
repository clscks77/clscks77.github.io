---
title: "[Note] 2025-04-28 Vision_paper_review"
categories: [Notepad, Daily]
tags: [diffusion]
date: 2025-04-28 00:00:21 +0900
comments: false
excerpt: "비전 09주차. Introduction to Diffusion models (DDPM, DDIM)"
--- 
---

- 일단, 과제용으로 대충 봄
- 근데, 개인연구 관련된 내용이라서 나중에 자세히 리뷰할 예정

---

## Denoising diffusion probabilistic models [NeurIPS'20]

■ Abstract (DDPM)
- 복잡한 고차원 데이터 분포를 모델링하여 새로운 샘플을 생성하는 생성 모델
    - GAN이나 VAE와 달리, 확률적 생성 과정을 직접적으로 모델링함
- 데이터를 점진적인 노이즈로 오염시키는 Forward Process 정의하고,
- 그 반대 방향으로 노이즈가 없는 데이터를 복원하는 Reverse Process를 학습함
    - DDPM은 노이즈를 제거하는 네트워크를 학습하는 것
- Diffusion이 작은 양의 가우시안 noise로 구성되어 있기 때문에 샘플링 chain을 조건부 가우시안으로 설정하는 것으로 충분하고, 간단한 신경먕으로 parameterize할 수 있다.


■ Diffusion model
- parameterized Markov chain을 학습시켜 유한 시간 후에 원하는 데이터에 맞는 샘플을 만드는 모델
- 기존 diffusion model은 정의하기 쉽고 학습시키기 효율적이지만 고품질의 샘플을 만들지 못함
- DDPM은 고품질의 샘플을 만들 수 있을 뿐만 아니라, GAN 같은 다른 생성 모델보다 더 우수한 결과를 보임
- diffusion model의 특정 parameterization이 학습 중 여러 noise 레벨에서의 denoising score matching과 비슷하며, 샘플링 중 Langevin dynamics 문제를 푸는 것과 동등하다는 것을 보임 ◆
    - Diffusion model이 다른 latent variable model과 다른 점은 forward process 혹은 diffusion process라 불리는 approximate posterior가 beta에 따라 가우시안 noise를 점진적으로 추가하는 Markov chain이라는 것


■ Forward Process
- 원본 데이터에 점차적으로 가우시안 노이즈를 추가하여 오염시키는 Markov Chain
    - Markov Chain: 현재 상태롭터 미래 상태를 결정할 수 있는 확률적 과정
- 최종적으로 완전히 랜덤한 가우시안 분포로 보냄
    - beta_t(베타 스케줄)는 아주 작은 값에서부터 점진적으로 커지는 하이퍼파라미터
        - beta_t가 커질 수록 노이즈 비율이 커짐


■ Training Objective
- 목표는 Evidence Lower Bound (ELBO)를 최대화하는 것
- 논문에서는 ELBO를 간단한 형태로 변형하여 최종 loss를 만듦
    - KL divergence는: 두 확률 분포 사이가 얼마나 다른지 재는 방법
- 따라서 신경망은 epsilon(added noise)을 예측하도록 학습시킬 수 있음


---

## Denoising Diffusion Implicit Models [ICLR '21]

■ Abstract (DDIM)
- Diffusion-Denoising process(DDPM)는 markovian을 기반으로 작동되는 메커니즘이었지만, DDIM은 non-markovian으로 움직임
- DDPM의 성능은 뛰어나지만, 1000번 가까운 denoising 스텝 필요한 만큼 느림
    - DDPM에서는 한 스텝을 이동할 때마다 매번 새로운 랜덤 노이즈를 샘플링하고 추가하여  샘플링이 매우 stochastic(불확실)하고 느렸음
- DDIM은 DDPM의 "stochastic diffusion process"를 변형하여 Deterministic하고 빠른 샘플링을 가능하게 함
    - Diffusion을 확률적 추적이 아닌, 부드러운 deterministic trajectory(부드럽고 결정적인 경로)로 변환
    - 즉, 매 스텝마다 불필요한 랜덤성을 제거하고, 이미 예측된 정보만으로 다음 스텝을 정확하게 계산하여 이동


■ Non-Markovian forward processes
- DDIM과 DDPM은 Marginal distribution은 같지만, inference distribution에서 DDPM은 markovian인 반면, DDIM은 non-markovian으로 표현됨
    - 즉, DDPM에서는 markovian으로 인해 xt는 xt-1로 부터 결정되었다면, DDIM에서는 xt와 x0를 같이 이용해서 xt-1을 결정하게 됨

■ Reverse Process
- x0를 예측한 순간, 이미 다음 단계 xt-1도 거의 결정적으로 결정할 수 있다.
- 랜덤 노이즈를 새로 샘플링할 필요 없이, 예측한 x0와 현재 상태 xt를 조합하여 다음 스텝으로 이동
- 이 과정을 반복하면, 복원 경로가 부드럽고 일관된 흐름을 따라가면서 빠르게 깨끗한 데이터로 수렴할 수 있음


■ ODE (ordinary differential equation; 상미분방정식) 
- ODE는 "현재 상태"와 "시간"을 알면, "다음 상태"를 계산하는 식
- DDIM에서 해석하는 샘플링 과정 (=ODE를 numerical integration하는 것과 매우 유사)
    - 이미지가 점점 노이즈로 변하는 Forward Process를 관찰하면, 이건 시간에 따른 데이터의 "변화 속도"를 알 수 있는 것
    - 그렇다면, Reverse Process는 이 변화 속도를 "역방향"으로 따라가는 것
    - "속도를 따라 경로를 거꾸로 따라가는 과정"은, 수학적으로 ODE를 푸는 것과 동일한 문제
- DDIM Reverse Process = 특정한 ODE를 풀어서 깨끗한 데이터를 복원하는 과정


---

Denoising diffusion probabilistic models [NeurIPS'20]
- Forward Process가 Laplacian, 등의 다른 분포를 사용하면?

Denoising Diffusion Implicit Models [ICLR '21]
- non-markovian 관점이 유용한 이유?

