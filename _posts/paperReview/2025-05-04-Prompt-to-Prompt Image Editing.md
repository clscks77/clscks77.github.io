---
title: "[Gen] Prompt-to-Prompt Image Editing with Cross-Attention Control [ICLR, 2023]"
categories: [PaperReview, GenerativeAI]
tags: [vision, diffussion, image-editing]
date: 2025-05-04 18:57:12 +0900
comments: false
excerpt: "비전 12주차-Text-guided Image Editing 논문 리뷰"
--- 
---


```
To this end: 이를 위해
phenomenal: 경이적인
```


---

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
    - **cross-attention layer**가 이미지의 spatial layout과 prompt의 각 단어 간의 **'관계를 제어하는 핵심 요소임**'을 관찰함
- 이를 바탕으로 textual prompt만 편집하여 이미지 합성을 모니터링하는 몇 가지 application을 보여줌
    - localized editing by replacing a word, global editing by adding a speciﬁcation, and even delicately controlling the extent(정도) to which a word is reﬂected in the image.
> 분석과 관찰을 기반으로 한 insight 멋지다



---

## 1 Introduction

- large-scale language-image (LLI) models (대규모 언어 이미지 모델)
    - Imagen [38], DALL·E 2 [33] and Parti [48]
    - 매우 큰 language-image 데이터셋에서 학습됨
    - auto-regressive model과 diffusion model을 포함한 SOTA 이미지 생성 모델을 사용
        > auto-regressive model은 뭐지?
- (한계) 편집 수단을 제공하지 않으며, 이미지의 특정 semantic 영역에 대한 제어가 부족함
    - 특히, 텍스트 프롬프트에 약간의 변화만 있어도 완전히 다른 출력 이미지로 이어질 수 있다.

- (기존 연구) LLI-based methods [28, 4, 33]
    - 사용자가 인페인팅(inpainting)할 이미지의 일부를 명시적으로 마스킹하고, 원래 이미지의 배경과 일치시키면서 마스킹된 영역에서만 변경되도록 함
        - 이 방식이 결과는 좋았지만, 마스킹 절차가 번거롭고, 빠르고 직관적인 텍스트 기반 편집을 방해함
        - 또한, 이미지 콘텐츠를 마스킹하면 중요한 structural information가 제거되며, 이는 인페인팅 프로세스에서 완전히 무시됨
    - 따라서 특정 개체의 texture(텍스처) 수정과 같은 일부 편집 능력은 인페인팅 범위를 벗어남
        > ◆ ???? 왜????

- (본 논문의 방법) Prompt-to-Prompt 조작을 통해 pre-trained 된 ext-conditioned diffusion model에서?
    - 이미지를 의미론적으로 편집하는 직관적이고 강력한 텍스트 편집(textual editing) 방법
    
- 이를 위해 cross-attention layer를 깊이 파고들고, 생성된 이미지를 제어하는 handle로서의 의미론적 강점(semantic strength)을 탐구함
    - 특히, 특히 prompt text에서 추출한 pixel과 token을 결합하는 **고차원 텐서인 내부 cross-attention map**을 고려했고,
    - 저자들은 이러한 맵이 생성된 이미지에 결정적으로 영향을 미치는 **풍부한 의미론적 관계를 포함**하고 있음을 발견함

- (핵심 아이디어) diffusion process 중에 cross-attention map을 주입하여 이미지를 편집할 수 있다는 것
    - 즉, 각 Diffusion step 중에 어떤 픽셀이 prompt text의 어떤 토큰에 attention 되는지 제어한다는 것

- 본 논문의 방법을 다양한 창의적 편집 애플리케이션에 적용하기 위해 간단하고 의미론적인 인터페이스를 통해 cross-attention map을 제어하는 몇 가지 방법을 보여준다.
    1. 장면 구성을 유지하기 위해 cross-attention map을 수정하는 동안 프롬프트에서 단일 토큰의 값을 변경 (?◆ ???)
    2. 이미지를 전체적으로 편집
    3. 생성된 이미지에서 단어의 의미론적 효과를 증폭하거나 약화시킴

- 본 논문의 접근 방식은 텍스트 프롬프트만 편집하여 직관적인 이미지 편집 인터페이스를 구성하며, Prompt-to-Prompt라고 부른다.
    - 모델 학습, finetuning, 추가 데이터 또는 최적화가 필요하지 않다.
    - 분석을 통해 편집된 프롬프트에 대한 충실도와 소스 이미지 사이의 trade-off를 인식하여 생성 프로세스에 대한 훨씬 더 많은 제어가 가능하다.  (?◆ ???)
    - 저자들은 기존의 reverse process를 사용하여 본 논문의 방법이 실제 이미지에 적용될 수 있음을 보여주었다. (?◆ ???)

---

## 2 Related work


---

## 3 Method



### 3.1 Cross-attention in text-conditioned Diffusion Models



### 3.2 Controlling the Cross-attention



---

## 4 Applications


---

## 5 Conclusions

---

## A Background

### A.1 Diffusion Models


### A.2 Cross-attention in Imagen

---

## B Additional results