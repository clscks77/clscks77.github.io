---
title: "[Gen] Prompt-to-Prompt Image Editing with Cross-Attention Control [ICLR, 2023]"
categories: [PaperReview, GenerativeAI]
tags: [vision, diffusion, image-editing]
date: 2025-05-04 18:57:12 +0900
comments: false
excerpt: "비전 12주차-Text-guided Image Editing 논문 리뷰"
--- 
---

- paper: <https://arxiv.org/abs/2208.01626>
- github: <https://github.com/google/prompt-to-prompt/>
- review ref: <https://jang-inspiration.com/prompt-to-prompt-image-editing-with-cross-attention-control>
- open review(必讀): <https://openreview.net/forum?id=_CDixzkzeyb>

```
To this end: 이를 위해
phenomenal: 경이적인
amplify or attenuate: 증폭 또는 감쇠
encompassing: 포함하다, 망라하다, 아우르다
auxiliary input: 보조 인풋
in tandem with CLIP: CLIP과 함께 (나란히, 협력하여)
Seminal works: 중대한 연구 (영향력이 큰)
revolutionary: 획기적인, 혁명적인, 혁신적인
curated: 전문적인 식견으로 엄선한
struggle over large datasets: 큰 데이터셋에서는 어려움을 겪음 
```


---

> 핵심 개념은 cross-attention map 조작을 통해 diffusion 모델이 이미지를 생성하는 과정을 세밀하게 제어할 수 있다는 것


## Abstract

large-scale text-driven synthesis models
- 주어진 텍스트 프롬프트에 따라 매우 다양한 이미지를 생성하는 모델
- 이를 텍스트 기반 이미지 **편집**으로 확장하고자 하는 연구
> 기존의 대세 연구를 확장하고자 한다

generative models
- 원본 이미지의 대부분을 보존하는 것이 편집 기술의 본질적인 특성
text-based models
- 텍스트 프롬프트를 조금만 수정해도 완전히 다른 결과가 나오기 때문에 편집이 까다로움
> 이 편집 연구가 까다로운 이유가 있다

State-of-the-art methods 
- 사용자가 공간 마스크를 제공하여 편집 위치를 지정하도록 요구함으로써 이러한 문제를 완화하고, 따라서 마스크된 영역 내의 원본 구조와 콘텐츠를 무시
> 현재 쏘타는 사용자가 공간 마스크를 지정하고, 이를 무시하도록 했다.

In this paper
- 저자는 텍스트만으로 편집을 제어하는 직관적인 Prompt-to-Prompt editing framework를 고집함
- 이를 위해(To this end)
    - text-conditioned model을 심층 분석하고,
    - **cross-attention layer**가 이미지의 spatial layout과 prompt의 각 단어 간의 **관계를 제어하는 핵심 요소임**을 관찰함
    > 분석과 관찰을 기반으로 한 insight 멋지다. 이게 academic이지
- 이를 바탕으로 textual prompt만 편집하여 이미지 합성을 모니터링하는 몇 가지 application을 보여줌
    - localized editing by replacing a word, global editing by adding a speciﬁcation, and even delicately controlling the extent(정도) to which a word is reﬂected in the image.



---

## 1 Introduction

- **large-scale language-image (LLI) models (대규모 언어 이미지 모델)**
    - Imagen [38], DALL·E 2 [33] and Parti [48]
    - 매우 큰 language-image 데이터셋에서 학습됨
    - **auto-regressive model**과 **diffusion model**을 포함한 SOTA 이미지 생성 모델을 사용
        > ◆ auto-regressive model은 뭐지?
- (한계) 편집 수단을 제공하지 않으며, 이미지의 특정 semantic 영역에 대한 제어가 부족함
    - 특히, 텍스트 프롬프트에 약간의 변화만 있어도 완전히 다른 출력 이미지로 이어질 수 있다.

- **(기존 연구) LLI-based methods [28, 4, 33]**
    > [28] Alex Nichol, et. el. Glide: Towards photorealistic image generation and editing with text-guided diffusion models [arXiv, 2021] [ICML, 2022]  
	> [4] Omri Avrahami, et. el. Blended latent diffusion [arXiv, 2022] [ACM TOG, 2023]  
	> [33] Aditya Ramesh, et. el. Hierarchical text-conditional image generation with clip latents [arXiv, 2022] (= OpenAI의 DALL·E 2논문)
    - 사용자가 인페인팅(inpainting)할 이미지의 일부를 명시적으로 마스킹하고, 원래 이미지의 배경과 일치시키면서 마스킹된 영역에서만 변경되도록 함
        - 이 방식이 결과는 좋았지만, **마스킹 절차가 번거롭고, 빠르고 직관적인 텍스트 기반 편집을 방해**함
        - 또한, 이미지 콘텐츠를 마스킹하면 **중요한 structural information가 제거**되며, 이는 인페인팅 프로세스에서 완전히 무시됨
    - 따라서 특정 개체의 texture(텍스처) 수정과 같은 일부 편집 능력은 인페인팅 범위를 벗어남
        > ◆ ???? 왜???? structural information이 없어져서 "특정 개체" 이게 안되는건가?

- (본 논문의 방법) Prompt-to-Prompt 조작을 통해 **pre-trained 된 text-conditioned diffusion model에서?**
    - 이미지를 **의미론적으로 편집하는** 직관적이고 강력한 텍스트 편집(textual editing) 방법
    > pre-trained 부분이 궁금함.  
    > text-conditioned diffusion model은 기존에 있던건가?
    
- 이를 위해 **cross-attention layer**를 깊이 파고들고, 이미지 생성을 제어하는 handle로서의 **의미론적 강점(semantic strength)**을 탐구
    - 특히, prompt text에서 추출한 pixel과 token을 결합하는 **고차원 텐서인 내부 cross-attention map**을 고려했고,
    - 저자들은 이러한 맵이 생성된 이미지에 결정적으로 영향을 미치는 **풍부한 의미론적 관계를 포함**하고 있음을 발견함
    > 탐구 결과: cross-attention layer **내부 cross-attention map**이 중요한 **의미론적 관계**를 포함하고 있었고, 이게 고품질 이미지 생성의 핵심이더라

- (핵심 아이디어) diffusion process 중에 cross-attention map을 주입하여 이미지를 편집할 수 있다는 것
    - 즉, 각 Diffusion step 중에 어떤 픽셀이 prompt text의 어떤 토큰에 attention 되는지 제어한다는 것
    > 그렇다면, cross-attention map을 Diffusion 과정 중에 따로 주입하여 이미지를 편집을 제어해보자? 이건가?

- 본 논문의 방법을 다양한 창의적 편집 애플리케이션에 적용하기 위해 간단하고 의미론적인 인터페이스를 통해 cross-attention map을 제어하는 몇 가지 방법을 보여준다.
    1. **prompt에서 단일 token 값을 변경(ex.개 → 고양이):** scene composition(장면 구성)을 유지하면서 cross-attention maps 수정
    2. **전체적으로 편집(globally edit; 스타일 변경):** prompt에 새로운 word를 추가하는데, 이전 token에 대한 attention은 고정하면서, 새 token으로 새로운 attention를 이동시킬 수 있게 함
    3. **word의 의미론적 효과를 증폭하거나 약화시킴(amplify or attenuate)**

- 본 논문의 접근 방식은 textual prompt만 편집하여 **직관적인 이미지 편집 인터페이스**를 구성하는 것 = Prompt-to-Prompt라고 부른다.
    - model training, finetuning, 추가 데이터 또는 최적화가 필요하지 않다.
        > 그럼 어떻게?? 상당히 흥미로운 부분◆
    - 저자들의 분석을 기반으로 보면, edited prompt 와 source image 사이의 ﬁdelity에 대한 trade-off를 익식함으로써, 이미지 생성 제어를 더 할 수 있다?
        > ◆ edited prompt 와 source image 사이의 ﬁdelity에 대한 trade-off를 익식한다?
    - 저자들은 기존의 inversion process를 사용하여 본 논문의 방법이 실제 real 이미지에 적용될 수 있음을 보여주었다.
        > ◆ ??? real image가 뭔데, 이때까지 말한건 real image가 아니였어?


> 여기까지 읽어보면, Cross-attention map은 신이고, Diffusion은 만능인데  
> text-pixel 사이의 semantic relations을 캡쳐할 수 있다니

---

## 2 Related work

- **text-driven image manipulation**은 최근 GANs [15, 8, 19–21] CLIP [32]과 함께 눈에 띈 발전을 이룸
    - GAN: 고품질 생성으로 잘 알려짐
    - CLIP: 의미적으로 풍부한 joint? image-text representation으로 구성, 수백만 개의 text-image pair를 통해 학습

1. **(using text only)** 이러한 구성 요소(GAN, CLIP)를 결합한 획기적인(Seminal) 연구들[29, 14, 46, 2]이 혁신적이었던 이유는,
    - 별도의 수작업(extra manual labor) 없이, 텍스트만을 사용하여 매우 사실적인 조작을 만들어 냈기 때문 
2. **(use masks)** Bau 등[7]은 사용자가 제공한 mask를 사용하여 텍스트 기반 편집을 localize하고(???), 특정 공간 영역(spatial region)으로 변경을 제한하는 방법을 시연함
    - **(한계)** GAN 기반 이미지 편집 접근 방식은 사람 얼굴과 같은 highly-curated datasets [27]에서는 성공적이지만, 크고 다양한 데이터 세트에서는 어려움을 겪음
3. **(GAN)** Crowson 등[9]은 더 표현력 있는(expressive) generation capability를 위해 다양한 데이터에 대해 학습된 VQ-GAN[12]을 백본으로 사용함
4. **(Diffusion)** [5, 22]에서는 최신 Diffusion model[17, 39, 41, 17, 40, 36]을 활용하는데, 이는 매우 다양한 데이터 세트에서 SOTA를 달성하며, 종종 GAN을 능가함[10]
5. **(global changes)** Kim 등[22]은 global changes를 수행하는 방법을 보여줌 
    > ◆ ??? 스타일 변경 같은건가? 이건 왜 언급한거임?
6. **(use masks)** Avrahami 등[5]은 사용자가 제공하는 마스크를 사용하여 성공적인 local manipulations을 수행함


- **(text only)** 마스크 없이 텍스트만 필요한 대부분의 작업은 글로벌 편집에 국한되어 있지만[9, 23], 
    - Bar-Tal 등[6]은 마스크를 사용하지 않고 텍스트 기반의 localized editing 기법을 제안하여 인상적인 결과를 보여주었음
        - 그러나 이 기법은 주로 텍스처를 변경할 수 있을 뿐 자전거를 자동차로 바꾸는 것과 같은 복잡한 구조를 수정할 수 없음
        - 또한, 본 논문의 방법과 달리 [6]의 접근 방식은 각 입력에 대해 네트워크를 훈련시켜야 함
    > text only는 주로 global 기반 연구[9, 23]이지만, local 기반의 text only 연구[6]도 있음.  
    > 하지만, 텍스처를 변경만 되고, 자전거 → 자동차 같은건 안됨 (◆ localized editing이 내가 생각하는 그런게 아닌가????)  
    > 또한, 각 입력에 대해 네트워크를 다시 훈련해야 한다는 점이 한계라고 볼 수 있음 (◆ source image마다 다시 학습해야 한다는 말인가??)

- 수많은 연구[11, 16, 42, 25, 26, 30, 31, 34, 49, 9, 13, 36]는 **text-to-image synthesis**이라고 알려진 plain text를 기반으로 한 이미지 생성 기술을 크게 발전시켰음
- **(LLI)** 최근 Imagen [38], DALL-E2 [33], Parti [48] 등 여러 large-scale text-image model이 등장하여 전례 없는 의미 생성을 보여주었음
    - 그러나 이러한 모델들은 생성된 이미지에 대한 제어 기능을 제공하지 않으며, 특히 text guidance만을 사용함
    - 이미지와 관련된 원래 프롬프트에서 단어 하나만 변경해도 완전히 다른 결과가 나오는 경우가 많음
        - 예를 들어, 'dog'에 형용사 'white'을 추가하면 개의 모양이 바뀌는 경우가 많음
    - 이를 극복하기 위해 몇몇 연구[28, 4]에서는 사용자가 변경 사항이 적용되는 영역을 제한하는 마스크를 제공한다고 가정함
- 이러한 기존 연구와 달리, 본 논문은 **생성 모델 자체의 내부 레이어에 있는 공간 정보**를 사용함으로써 텍스트 입력만 필요하도록 함 (mask X, requires textual input only)
    - 이는 사용자가 text prompt만 수정하여 local or global 세부 사항(details)을 수정하는, 훨씬 직관적인 editing experience를 제공함
    > ◆ 직관적이라는 건 알겠는데, 왜 마스크를 쓰면 안되는가에 대한 근거가 필요해 보이네

> 각 연구분야의 fundamental tasks 체크하기  
> 참.. 뭐랄까.. 이미지 생성&편집 기술은 이렇게 날로 발전하는데, 이를 악용하는 딥페이크 문제는 이러한 발전을 따라잡을 수 있는가? 이쪽 연구를 알아갈 수록 걱정이 커지네...

---

## 3 Method

- text-guided diffusion 모델 `Imagen`[38]이 text prompt `P`와 random seed `s`를 사용하여 생성한 이미지를 `I`라고 가정하자.
- (목표) 본 논문의 목표는 편집된 prompt `P∗`로만 guided하여, 입력 이미지 `I`를 편집한 `편집된 이미지 I∗`를 생성하는 것이다.
    - 사용자가 정의한 마스크에 의존하지 않으려 함
    > [38] Photorealistic text-to-image diffusion models with deep language understanding [NIPS, 2022]  
        > 이게 SOTA였나?  
        > "텍스트 전용 코퍼스로 사전 학습된 일반적인 대규모 언어 모델(예: T5)이 이미지 합성을 위한 텍스트 인코딩에 놀라울 정도로 효과적이라는 것"

![attention injection](\assets\img\paper\P2P_attention_injection.PNG)
_그림 2: Content modiﬁcation through attention injection_

```
위쪽 행: diffusion 과정 동안에 원본 이미지 'lemon cake'의 attention weights를 주입 
아래 행: attention weights를 주입하지 않고, 원본 이미지와 동일한 random seed만 사용 (◆ 이게 밑에서 말하는 실패한 시도인가?)
후자는 원본 이미지와 거의 관련이 없는 완전히 새로운 구조를 만듦
```

- (실패한 시도) 내부 무작위성(internal randomness)을 고정(fix)하고 edited text prompt를 사용하여 다시 생성하는 방법은 간단하지만,
    - fig. 2에서 볼 수 있듯이, 이렇게 하면 구조(structure)와 구성(composition)이 완전히 다른 이미지가 생성됨
        > ◆ 즉, 초기 노이즈를 대표하는 random seed를 고정했으니, 다시 이미지를 생성했을 때 원래 구조와 비슷한 이미지를 기대할 수 있지 않을까?했는데,  
        > 시드를 고정하더라도 텍스트를 바꾸면, **모델은 여전히 이미지를 처음부터 새로 생성**하기 때문에, 텍스트가 조금만 바껴도 cross-attention 경로는 완전히 달라지고, 구조(composition), 형태(geometry)**가 전혀 다르게 나옴  
        > **즉, random seed를 고정한 것만으로는 원하는 부분만 살짝 바꾸는 식의 편집(editing)은 되지 않는다는 것**
- (Our key observation) 여기서 중요한 점은 
    - 생성된 이미지의 **구조(structure)와 모양(appearances)**이 random seed뿐만 아니라 **diffusion 과정 중에 나타나는 pixel과 text 임베딩 간의 interaction**에 따라 달라진다는 것
        > 텍스트가 바뀌면 pixel-to-token 관계도 완전히 달라지기 때문에 결과 이미지의 구조(structure)와 모양(appearances)이 깨지는 것  
        > 즉, diffusion은 **텍스트를 기준으로 픽셀을 구성**하는 과정으로 해석할 수 있음
- (따라서) 본 논문은 **cross-attention layers에서 발생하는 pixel-to-text interaction을 수정**하는 Prompt-to-Prompt image editing 제안
    - 구체적으로, **입력 이미지 `I`의 cross-attention maps을 삽입**하면 원래의 구조(structure)와 구성(composition)를 유지할 수 있다.


확산 모델에 대한 추가 배경 지식은 부록 A를 참조하세요.

> (정리) 이미지 I는 텍스트 프롬프트 P로부터 생성된 결과이고,  
- 이 이미지 생성 중에 계산된 **cross-attention map M**은 **"어떤 픽셀이 어떤 단어를 따랐는가"를 담고 있음**
- 편집된 프롬프트 P*로 새 이미지를 생성할 때, **새로운 텍스트 토큰에 대한 attention은 자유롭게 계산**되도록 놔두고,
- **기존 토큰에 대한 attention은 이전 것(M)을 강제로 다시 사용**하면, 구조가 깨지지 않음
    - ◆ 이거, 수식적으로 좀 봐야겠따

---


### 3.1 Cross-attention in text-conditioned Diffusion Models

- text-conditioned Diffusion Models에서 cross-attention이 어떻게 사용되는지 분석 결과 review
> text-conditioned Diffusion Models이 뭔데, Imagen 같은 모델인가?

- backbone = text-guided synthesis model인 Imagen [38]
    - "Since the **composition and geometry** are mostly determined at the **64 × 64 resolution**, we only adapt the text-to-image diffusion model, **using the super-resolution process as is**"
    > 최신 text-to-image diffusion 모델은 일반적으로 **다중 해상도(multi-resolution) 구조**를 사용함.  
    > 또한 형태적 결정(composition and geometry)은 낮은 해상도 (64x64) 단계에서 대부분 정해짐.  
    > 256x256, 1024x1024 등의 super-resolution에서는 세부 정보만 보강하고, 구조는 건드리지 않음.  
    > 따라서, 본 논문의 취지에 맞게 구조를 바꾸는 저해상도 단계만 수정하겠다.

- (Background of Diffusion step) 각 diffusion step `t`는 U-shaped 네트워크[37]를 사용하여 노이즈가 있는 이미지 `z_t`와 텍스트 임베딩 `ψ(P)`로부터 노이즈 `ε`을 예측하는 것으로 구성됨
    - 마지막 step에서 이 프로세스는 생성된 이미지 `I = z_0`을 산출함 
        > U-shaped 네트워크[37] U-Net: Convolutional Networks for Biomedical Image Segmentation [MICCAI, 2015]

> ◆ 근데 U-shaped network [37] 이 논문은 왜 언급됐지? Imagen이 이 논문을 기반으로 했나?
- Imagen에서 사용하는 **noise prediction network(ε_theta)**가 U-Net 구조이기 때문
- U-Net은 이미지의 전체 정보(저해상도 의미 정보)와 세부 정보(고해상도 디테일)를 효과적으로 결합하기 위해 만들어짐 
- 거의 **모든 diffusion 모델 (DDPM, Stable Diffusion, Imagen 등)**이 노이즈 예측기 ε_theta를 U-Net 구조로 구성함
- Prompt-to-Prompt는 이 U-Net 내부의 cross-attention layer를 조작함으로써 작동함

- 가장 중요한 점은 두 modality(text-visual) 간의 상호 작용(interaction)이 **noise 예측 과정**중에 발생한다는 점인데, 이 과정에서,
    - **각 textual token에 대한 spatial attention map**을 만들어내는 **Cross-attention layer**가 활용된다.
        - 즉, visual featuer와 textual feature의 임베딩이 Cross-attention layer를 통해 융합된다.

> ◆ diffusion 모델 내부에서 "텍스트와 이미지가 어떻게 만나는가?"에 대한 설명
- 텍스트(Text)와 이미지(Image)의 상호작용(interaction)이 정확히 어떤 구조를 통해 일어나는지
- Diffusion 모델은 각 단계에서 노이즈가 섞인 이미지 z_t에서 "다음 상태 z_{t-1}로의 노이즈 ε"을 예측하고 제거함
    - 이 noise prediction 단계에서, 텍스트 임베딩이 하난의 조건으로 들어가기 때문에, 텍스트가 이미지 생성에 영향(interaction)을 주는 시점이라고 한거
- 그리고 이 시점에서, 모델은 "이 픽셀은 어떤 단어를 참고해서 생성할지"를 학습하는데,
    - 이미지의 latent feature φ(z_t)가 텍스트 임베딩 ψ(P)을 참고해서 cross-attention layer에서 결합하는 방식으로 처리됨
    - Cross-attention의 결과물은 각 텍스트 토큰이 이미지의 어떤 공간(spatial location)에 영향을 주는지를 나타내는 attention map임
        - 예를 들어, "apple"이라는 단어에 대한 attention map은 사과가 위치할 이미지 영역에 집중될 것이고,
        - "red"는 사과 전체에 퍼져 있을 수도 있음

![P2P Method overview](\assets\img\paper\P2P_Method_overview.PNG)
_ﬁg. 3 Method overview_

```
- Top: 각 textual token에 대한 spatial attention map을 생성하는 cross-attention layer를 통해 visual 및 textual embedding을 융합
    - ◆ attention map M을 이용해서 textual embedding Q와 visual embedding K, V를 융합시킨다는건가? K,V가 뭔지 모르겠네
- Bottom: 소스 이미지의 attention maps을 사용하여 생성된 이미지의 spatial layout(구성)과 geometry(형상)를 제어
    - 이를 통해 textual prompt만 편집하여 다양한 편집 작업을 수행할 수 있음
        1) prompt에서 word를 교체할 경우, 소스 이미지 map M_t를 타깃 이미지 map M_t*보다 우선하여 삽입하여 spatial layout 유지 
        2) 새 문구(phrase)를 추가하는 경우, prompt에서 변경되지 않은 부분에 해당하는 맵만 삽입합니다. 
            - ◆ 엥 반대 아니고? 안바뀐 prompt 부분을 타깃 이미지 map M_t*로 바꾼다고? 소스 이미지 map M_t가 아니라?
        3) 단어의 효과를 증폭/약화시키는 경우, 대응되는 attention map의 가중치를 re-weighting합니다.
```

- (ﬁg. 3 참고) 공식적으로(formally) 보면, 
    - **노이즈가 있는 이미지 `φ(z_t)`의 deep spatial feature**는 query 행렬 `Q = 𝓁_Q(φ(z_t))`에 projected 되고, 
        > `z_t`: 현재 diffusion 단계의 노이즈 이미지  
        > `φ(z_t)`: 그 이미지에 대한 deep feature (CNN 같은 인코더가 추출한 spatial feature)  
        > `𝓁_Q`: 학습된 선형 변환(linear projection) → ◆ 이게 뭔지 모르겠네  
        > `Q` = 이미지의 각 픽셀은 쿼리(Query)가 됨 → "이 픽셀은 어떤 단어를 참고해야 할까?" 라고 묻는 것  
    - **textual embedding**은 key 행렬 `K = 𝓁_K(ψ(P))`와 value 행렬 `V = 𝓁_V(ψ(P))`에 projected됨
        > `ψ(P)`: 텍스트 프롬프트 `P`의 토큰 임베딩  
            - 예: "a red apple" → [a], [red], [apple]  
            - 이걸 임베딩하면 각 단어는 고정된 벡터 공간의 점이 되고, 이게 ψ(P)  
            - 그리고 각 단어(token) 임베딩을 두 가지로 나눠서 처리 K (Key), V (Value)  
            - 하나의 프롬프트에서 Key/Value를 뽑아내기 위해 **각각의 선형 변환(linear projection)**을 학습하는거
    - (학습된 linear projection `𝓁_Q`, `𝓁_K`, `𝓁_V`를 통해)
        > ◆ `ℓ_K(x) = W_K * x + b_K`  
        > attention이 잘 작동하도록 diffusion loss (ex. noise prediction 오류)를 줄이는 방향으로 전체 모델 학습 중에 같이 학습
- 그럼 attention maps M은 수식(1)과 같아짐: `M = softmax(QKᵀ / √d)`  
    > `QKᵀ`: 각 이미지 픽셀의 Query와, 각 단어의 Key 간의 **유사도(similarity)**를 계산  
    > `d`: 키/쿼리 벡터의 차원 수(latent projection dimension) → scale을 위해 √d로 나눔  
    > `softmax`: attention weight로 바꾸기 위해 정규화  
    > `Mᵢⱼ`: "i번째 픽셀은 j번째 단어에 얼마나 집중해야 할까?"를 나타내는 가중치
- 마지막으로, cross-attention output `φ_hat(zt) = MV`로 정의되며, 이는 spatial feature `φ(zt)`를 업데이트하는 데 사용됨
    > 각 픽셀은 **단어별 가중치(M)**를 바탕으로 **의미 정보(V)**를 받아들임  
    > `φ(zt)`는 텍스트 의미가 반영된 이미지 피처로 업데이트됨

- 직관적으로, cross-attention output MV는 weight가 attention maps M인 value V의 가중 평균(weighted average)이고, 이는 Q와 K 사이의 **similarity**와 상관관계가 있다.
- 실제로는 표현력(expressiveness)을 높이기 위해 multi-head attention[44]을 병렬로 사용한 다음, 결과를 concatenated하고 학습된 linear layer을 통과하여 최종 출력을 얻음
    > multi-head attention[44]  
        - 하나의 attention head는 하나의 방식으로만 정보를 집중할 수 있음  
        - 이미지나 텍스트는 복잡한 구조를 가지므로, 여러 개의 head가 병렬로 각기 다른 방식으로 attention을 수행하게 함 (보통 8~12)  
            - 예: 하나는 색깔, 하나는 모양, 하나는 위치에 주목할 수 있음  
            - `ℓ_Q^1, ℓ_Q^2, ..., ℓ_Q^h` 이렇게 독립적인 linear projection을 사용하고, 독립적으로 attention 계산

```
Imagen [38], similar to GLIDE [28], conditions on the text prompt in the noise prediction of each diffusion step (see appendix A.2) through two types of attention layers: i) cross-attention layers. ii) hybrid attention that acts both as self-attention and cross-attention by simply concatenating the text embedding sequence to the key-value pairs of each self-attention layer.
```

- Imagen은 각 diffusion step에서 노이즈를 예측할 때, text prompt 정보를 조건(conditioning)으로 사용하는데,
- 이 conditioning은 attention layer 두 가지 방식으로 들어감
	1. cross-attention layers
		- pixel이 어떤 text에 주목할지 정하는 attention (앞에서 설명한 수식1)
	2. hybrid attention
		- self-attention + cross-attention
		- `K, V = concat(ℓ_K(x), ℓ_K(ψ(P)))     ← [image features | text embeddings]`

```
Throughout the rest of the paper, we refer to both of them as cross-attention since our method only intervenes in the cross-attention part of the hybrid attention. That is, only the last channels, which refer to text tokens, are modiﬁed in the hybrid attention modules.
```

- 본 논문에서는 hybrid attention의 cross-attention 부분에만 개입하기 때문에, 위 두 가지를 모두 cross-attention이라고 부름
    - 즉, text token을 참조하는 마지막 채널만 hybrid attention 모듈에서 수정됨
        > ◆ 여기서 이미지끼리 주목하는 부분(self-attention)은 그대로 두고, 이미지가 텍스트를 참조하는 부분(cross-attention)만 조작하겠다는 거




---


### 3.2 Controlling the Cross-attention

```
We return to our key observation — the spatial layout and geometry of the generated image depend on the cross-attention maps. This interaction between pixels and text is illustrated in ﬁg. 4, where the average attention maps are plotted. As can be seen, pixels are more attracted to the words that describe them, e.g., pixels of the bear are correlated with the word “bear”. Note that averaging is done for visualization purposes, and attention maps are kept separate for each head in our method. Interestingly, we can see that the structure of the image is already determined in the early steps of the diffusion process.
```

- 논문의 주관찰: 생성된 이미지의 **형태(geometry)**와 **배치(spatial layout)**는 cross-attention map의 구성에 결정적으로 의존한다.
	 - 즉, 어떤 픽셀이 어떤 단어에 집중하느냐에 따라, 그 픽셀이 어떤 객체의 일부가 될지, 이미지 내 어디에 배치될지, 어떤 경계를 형성할지...
- 그림4를 보면, "언어적 의미 → 이미지 공간"에 대한 연결이 attention을 통해 이루어짐을 알 수 있음
	- 곰을 설명하는 단어는 곰 영역의 픽셀과 연결됨
- 평균(attention map averaging)은 시각화를 쉽게 하기 위한 것일 뿐, 실제 모델은 다양한 head별 attention을 따로 유지하면서 fine-grained하게 조절함
	→ Multi-head attention의 정보 손실 없이 유지
	> 시각화해주기 위해서, 여러 head의 attention을 평균하여 표시를 했다. 근데, 실제로는 head별 attention을 따로 유지했다.
- ★ 이미지의 구조가 확산 과정의 초기 단계에서 이미 결정되어 있음을 알 수 있음
	- 이미지의 전체 구성과 형태(geometry)는 초반 few steps 안에 이미 대부분 결정됨
	> ★ 즉, attention injection을 초반 몇 step까지만 적용해도 구조를 보존할 수 있음

```
Since the attention reﬂects the overall composition, we can inject the attention maps M that were obtained from the generation with the original prompt P, into a second generation with the modiﬁed prompt P∗. This allows the synthesis of an edited image I∗ that is not only manipulated according to the edited prompt, but also preserves the structure of the input image I. This example is a speciﬁc instance of a broader set of attention-based manipulations leading to different types of intuitive editing. We, therefore, start by proposing a general framework, followed by the details of the speciﬁc editing operations.
```

- 핵심 조작 방법 = attention injection
    - 원본 prompt P의 cross-attention map M을 **초기 step 동안** 강제로 다시 사용하면
    - 새로운 텍스트에 따라 **이미지 내용을 바꾸긴 하되**, 기존 이미지 **구조를 가능한 유지**하도록 attention flow를 제한적으로 제어 가능하다
        > 왜냐? 이미지의 전체 구성과 형태는 초반 few steps의 cross-attention map으로 대부분 결정되니까!  
        > 저자들은 이런걸 "의미있는 좋은 편집"이라고 생각하는 듯  
        > ◆ 그럼 구조나 형태를 바꾸고 싶으면 어캄?? step 위치를 바꾸나?
    - 하지만, 위와 같은 attention injection은 하나의 활용 예일 뿐, attention map은 더 다양하게 활용 가능하다는 것
        - 특정 단어 강조/약화 (attention reweighting), 구조 제어 (timestamp 조절 ?) 등
        - 이후 편집 알고리즘의 일반 구조 (Algorithm 1)를 소개할 예정이다

```
Let DM(zt, P, t, s) be the computation of a single step t of the diffusion process, which outputs the noisy image zt−1, and the attention map Mt (omitted if not used). We denote by DM(zt, P, t, s){M ← M} the diffusion step where we override the attention map M with an additional given map M, but keep the values V from the supplied prompt. We also denote by M∗ t the produced attention map using the edited prompt P∗. Lastly, we deﬁne Edit(Mt,M∗ t , t) to be a general edit function, receiving as input the t’th attention maps of the original and edited images during their generation.
```


```
Our general algorithm for controlled image generation consists of performing the iterative diffusion process for both prompts simultaneously, where an attention-based manipulation is applied in each step according to the desired editing task. We note that for the method above to work, we must ﬁx the internal randomness. This is due to the nature of diffusion models, where even for the same prompt, two random seeds produce drastically different outputs. Formally, our general algorithm is:
```



```
Notice that we can also deﬁne image I, which is generated by prompt P and random seed s, as an additional input. Yet, the algorithm would remain the same. For editing real images, see section 4. Also, note that we can skip the forward call in line 7 by applying the edit function inside the diffusion forward function. Moreover, a diffusion step can be applied on both zt−1 and z∗ t in the same batch (i.e., in parallel), and so there is only one step overhead with respect to the original inference of the diffusion model. We now turn to address speciﬁc editing operations, ﬁlling the missing deﬁnition of the Edit(Mt,M∗ t , t) function. An overview is presented in ﬁg. 3(Bottom).
```


```
[Word Swap.] In this case, the user swaps tokens of the original prompt with others, e.g., P =“a big red bicycle” to P∗ =“a big red car”. The main challenge is to preserve the original composition while also addressing the content of the new prompt. To this end, we inject the attention maps of the source image into the generation with the modiﬁed prompt. However, the proposed attention injection may over constrain the geometry, especially when a large structural modiﬁcation, such as “car” to “bicycle”, is involved. We address this by suggesting a softer attention constrain: where τ is a timestamp parameter that determines until which step the injection is applied. Note that the composition is determined in the early steps of the diffusion process. Therefore, by limiting the number of injection steps, we can guide the composition of the newly generated image while allowing the necessary geometry freedom for adapting to the new prompt. An illustration is provided in section 4. Another natural relaxation for our algorithm is to assign a different number of injection timestamps for the different tokens in the prompt. In case the two words are represented using a different number of tokens, the maps can be duplicated/averaged as necessary using an alignment function as described in the next paragraph.
```



```
[Adding a New Phrase.] In another setting, the user adds new tokens to the prompt, e.g., P =“a castle next to a river” to P∗ =“children drawing of a castle next to a river”. To preserve the common details, we apply the attention injection only over the common tokens from both prompts. Formally, we use an alignment function A that receives a token index from target prompt P∗ and outputs the corresponding token index in P or None if there isn’t a match. Then, the editing function is given by: Recall that index i corresponds to a pixel value, where j corresponds to a text token. Again, we may set a timestamp τ to control the number of diffusion steps in which the injection is applied. This kind of editing enables diverse Prompt-to-Prompt capabilities such as stylization, speciﬁcation of object attributes, or global manipulations as demonstrated in section 4.
```


```
[Attention Re–weighting.] Lastly, the user may wish to strengthen or weakens the extent to which each token is affecting the resulting image. For example, consider the prompt P = “a ﬂuffy red ball”, and assume we want to make the ball more or less ﬂuffy. To achieve such manipulation, we scale the attention map of the assigned token j∗ with parameter c ∈ [−2, 2], resulting in a stronger/weaker effect. The rest of the attention maps remain unchanged. That is: As described in section 4, the parameter c allows ﬁne and intuitive control over the induced effect.
```



---

## 4 Applications


---

## 5 Conclusions


**(학문적인 발견)**
- 이 연구에서는 텍스트-이미지 융합 모델 내에서 교차 주의 레이어의 강력한 기능을 발견했습니다. 
- 이러한 고차원 레이어는 텍스트 프롬프트의 단어를 합성 이미지의 공간 레이아웃에 연결하는 데 중요한 역할을 하는 공간 지도를 해석 가능한 형태로 표현한다는 사실을 보여주었습니다. 
- 이 관찰을 통해 프롬프트의 다양한 조작이 합성 이미지의 속성을 직접 제어할 수 있음을 보여줌으로써 로컬 및 글로벌 편집을 포함한 다양한 응용 분야로 나아갈 수 있는 길을 열었습니다.

**(현실적인 의의)**
- 이 작업은 텍스트의 의미적 힘을 활용하여 사용자에게 간단하고 직관적인 이미지 편집 수단을 제공하기 위한 첫 번째 단계입니다. 
- 이를 통해 사용자는 텍스트를 조작할 때마다 원하는 이미지를 처음부터 생성하는 것이 아니라 각 단계마다 점진적인 변화를 보이는 시맨틱 텍스트 공간을 탐색할 수 있습니다. 

**(한계점)**
- 첫째, 현재의 반전 프로세스는 일부 테스트 이미지에서 눈에 띄는 왜곡을 초래합니다. 
    - 또한 반전하려면 사용자가 적절한 프롬프트를 제시해야 합니다. 이는 복잡한 구도의 경우 어려울 수 있습니다.
    - 텍스트 유도 확산 모델에 대한 반전 문제는 앞으로 철저히 연구해야 할 과제입니다. 
- 둘째, 현재 관심도 맵은 교차 관심도가 네트워크의 병목 지점에 위치하기 때문에 해상도가 낮습니다. 
    - 이로 인해 더욱 정밀한 로컬라이즈드 편집을 수행하는 데 한계가 있습니다. 
    - 이를 완화하기 위해 고해상도 레이어에도 크로스 어텐션을 통합할 것을 제안합니다.
    - 이 작업은 현재 저희의 역량을 벗어난 훈련 절차를 분석해야 하므로 향후 작업으로 남겨두겠습니다. 
- 마지막으로, 현재 방법으로는 이미지에서 기존 오브젝트를 공간적으로 이동하는 데 사용할 수 없음을 인식하고 향후 작업을 위해 이러한 종류의 제어도 남겨두었습니다.


---

## A Background

```
- auto-regressive model과 diffusion model을 포함한 SOTA 이미지 생성 모델을 사용해서
- large-scale language-image (LLI) models (대규모 언어 이미지 모델): Imagen [38], DALL·E 2 [33] and Parti [48]
- 이미지 편집 연구? LLI-based methods [28, 4, 33]

```

### A.1 Diffusion Models


### A.2 Cross-attention in Imagen

---

## B Additional results