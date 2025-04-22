---
title: "[Note] 2025-04-18 language_model_study.txt"
categories: [Notepad, Daily]
tags: [llm, study]
date: 2025-04-18 18:16:08 +0900
comments: false
excerpt: "LM 종류 스터디: encoder-only, decoder-only, encoder-decoder"
--- 
---

- **용도**

구조 | 특징 | 주요 용도 | 대표 모델
**Encoder-only** | 문맥 인코딩(이해)에 최적화 | 텍스트 분류, 문장 임베딩 | BERT, RoBERTa
**Decoder-only** | 생성에 최적화 | 텍스트 생성(에세이, 스토리), 오토컴플리션 | GPT, GPT-2, GPT-3
**Encoder-Decoder** | 입력→출력 매핑(변환) | 번역, 요약, 질문 생성, 스타일 변경 | T5, BART, mBART

- **특징**
  
특성 | **Encoder-only** | **Decoder-only** | **Encoder-Decoder**
방향성 | 양방향 | 단방향 (left-to-right) | 혼합
주요 Task | 이해 (분류 등) | 생성 | 변환 (입력→출력)
Pretraining 방식 | MLM | AutoRegressive LM | Denoising / Seq2Seq
학습 예 | BERT | GPT | T5
생성 능력 | 약함 | 강함 | 강함
파인튜닝 방식 | Fine-tune head | Prompt/Adapter tuning | End-to-end tuning


- BERT → GPT → T5는 구조적 진화를 보여줌
  - 이해 중심 → 생성 중심 → 범용적 텍스트 변환
- 최근 추세는 Decoder-only로 통합 (e.g., GPT-4, LLaMA2) → 효율성 + 범용성
     - RAG (Retrieval-Augmented Generation), Fusion-in-Decoder 등으로 구조 융합도 진행 중
- Multimodal LM은 보통 Encoder-Decoder 기반이 많음 (e.g., Flamingo, GIT, Gemini)


---

## Encoder-only

- Transformer의 인코더 블록만 사용
- Bidirectional Attention 사용 (e.g. Multi-layer bidirectional self-attention encoder)
- 주어진 문장의 모든 토큰 간 관계를 고려하여 토큰 임베딩을 생성 → 문맥 이해에 강력함
    - 단, 한쪽 방향의 디코딩이 없기 때문에 생성에 부적합함
- Pretraining 방식: MLM (Masked Language Modeling)
    - 입력 문장에서 일부 단어를 [MASK] 토큰으로 가리고, 모델이 그 가려진 단어를 예측하도록 학습
        - 문맥을 양방향으로 이해하는 데 특화
- 대표 모델
    - BERT (2018): Masked LM, Next Sentence Prediction
    - RoBERTa (2019): BERT 개선판 (더 큰 데이터셋, 더 긴 학습)
- 문장 분류 (감정 분석, 주제 분류 등), NER, POS 태깅, 검색 쿼리 임베딩에 활용

```
입력: "나는 [MASK] 먹고 싶다."
출력: "밥"
```

---

## Decoder-only

- Transformer의 디코더 블록만 사용
- 자기 자신 이전 토큰만 참조하는 Autoregressive(순차적) 방식 
- Unidirectional Attention (left-to-right)
- 강력한 생성 능력 (텍스트 예측, 문장 생성, 코딩 등)
    - 단, 문장 전체 문맥을 양방향으로 보지 못함 (이해보다는 생성 중심)
- Pretraining 방식: AutoRegressive LM
    - 시퀀스를 왼쪽에서 오른쪽으로 보며, 이전까지의 모든 단어를 기반으로 다음 단어를 예측
- 대표 모델
    - GPT-1, 2, 3, 4 (OpenAI)
    - LLaMA (Meta)
    - Mistral, Falcon, BLOOM
- 텍스트 자동 완성, AI 챗봇, 코딩 보조 (Codex, StarCoder)

```
입력: "나는 밥을"
출력: "먹었다"
```

---

## Encoder-Decoder (Seq2Seq)

- 입력 시퀀스를 인코더에서 인코딩하고, 디코더가 출력 시퀀스를 생성
  - 디코더는 인코더 출력과 자신의 이전 출력을 모두 참조함
- Encoder-Decoder 구조의 핵심은 Cross-Attention
  - Decoder의 Query → Encoder의 Key, Value로부터 정보를 끌어옴
  - Encoder의 출력(hidden states)을 cross-attention으로 참조
- 입력과 출력의 매핑에 최적화 → 텍스트 재작성, 변환 작업에 강함
    - 단, 구조가 무겁고 복잡하며, 학습 시간 및 리소스 소모가 큼
- Pretraining 방식: Denoising / Seq2Seq
    - 입력 문장을 인위적으로 손상(Denoising) → 모델이 복원 or 새로운 표현을 생성
        - 다양한 변형 방식으로 일반화 성능 확보
    - BART 방식 (Denoising Autoencoder)
        - 입력 문장을 섞거나, 삭제하거나, 마스킹 → 원래 문장 복원
    - T5 방식 (Text-to-Text Transfer)
        - 모든 태스크를 "텍스트 입력 → 텍스트 출력" 형태로 재정의
- 대표 모델
    - BART (Bidirectional and Auto-Regressive Transformer)
    - T5 (Text-To-Text Transfer Transformer)
    - mT5, mBART (다국어 버전)

모델 | **Encoder** | **Decoder** | **특징**
**T5** | 자체 설계 Encoder (BERT와 유사) | 자체 설계 Decoder (GPT 유사) | 모든 태스크를 text-to-text로 처리
**BART** | BERT-style bidirectional encoder | GPT-style decoder + cross-attn | BERT + GPT 개념에 가장 가까운 구조
**mBART** | 다국어 확장 버전 | BART 구조 기반 | 번역, 다국어 생성에 강함


```
// BART
"나는 밥을 먹었다" → "나는 [MASK] 먹었다" → 모델이 복원

// T5
입력: "summarize: 오늘은 날씨가 맑고 따뜻합니다."
출력: "맑고 따뜻한 날씨"
```

<div class="tip-box tip-blue">
    <i class="fas fa-lightbulb tip-icon"></i>
    TODO <br>
    - Diffusion Model 안 쓸 것 같지만, 공부는 따로 해두자 <br>
    - VAE 상세하게 스터디 (Encoder, Decoder, ELBO_loss) + latent space
</div>


