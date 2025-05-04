---
title: "[Gen] Uncovering Latent Chain of Thought Vectors in Language Models [ICLR Workshop 2025]"
categories: [PaperReview, GenerativeAI]
tags: [llm, latent, chain-of-thought]
date: 2025-05-02 19:03:21 +0900
comments: false
excerpt: "LM의 activation space을 조작하는 새로운 방식의 모델 스티어링(steering) 방법론"
--- 
---





언어 모델의 활성화 공간(activation space)을 조작하여 Chain-of-Thought (CoT) reasoning을 유도하는 새로운 방법
- 자연어 기반 CoT 프롬프트 없이도 모델이 복잡한 문제를 단계적으로 풀도록 유도할 수 있음을 보여주며, 
- 이는 추론 성능 향상과 계산 효율성 확보라는 두 가지 목표를 달성함
    - 자연어 프롬프트 없이도 CoT 수준의 성능을 실현
    - GSM8k, MMLU, AGI Eval, ARC Challenge 등 여러 벤치마크에서 경쟁력 있는 결과 도출
    - 모델 대상: Llama3 8B Instruct, Mistral 7B v0.2 Instruct.

----------------


- Weight-space vs Activation-space 조작
    - ROME (Meng et al., 2022): 모델 가중치 조작으로 특정 사실만 수정 가능.
    - LoRA (Hu et al., 2021): 저랭크 업데이트로 효율적 fine-tuning.
    - Plug-and-Play LM (Dathathri et al., 2020): 텍스트 생성 중 분류기 피드백을 통해 활성화 조작.
    - Steering Vectors (Subramani et al., 2022; Turner et al., 2023): 학습된 벡터를 활용해 특정 성향(예: 감정, 독성)을 유도.

----------------


LM의 *활성화 공간(activation space)"을 조작하는 새로운 방식의 “모델 스티어링(steering)” 방법론

- Prompt Engineering은 “Let's think step by step” 또는 “Explain your reasoning” 등의 문장을 프롬프트에 넣음.
- 이 논문은 그런 문장을 입력에 넣지 않고도 CoT 스타일의 추론을 유도하는 방법을 탐구
    - “CoT 프롬프트”가 유도하는 내부 활성화 패턴을 학습해서 그걸 직접 삽입하는 방식

> 모델 구조나 가중치(weight)는 건드리지 않음

- PyTorch hook을 사용해 inference(추론) 중에 특정 layer의 residual stream에 벡터를 삽입

----------------

1 INTRODUCTION & RELATED WORKS

- 언어 모델(LM)은 여전히 추론과 정확성 측면에서 어려움을 겪고 있음 (Huang et al., 2024; Mondorf & Plank, 2024). 








----------------


2 METHODOLOGY AND EXPERIMENTAL SETUP 2.1 STEERING VECTOR DERIVATION To derive steering vectors from LM layer activations, we employ a contrastive approach inspired by Panickssery et al. (2024). Let D denote the data-generating distribution over questions. In our experiments, D is instantiated by a corpus of roughly 300 logic questions drawn from established reasoning benchmarks, composed as follows: • Big Bench Lite: 3 questions from each category (72 total), • MMLU: 2 questions from each category in the development set (125 total), • GSM8k: 100 questions from the training set. For each question q ∼ D, we create two contrasting inputs by appending a specific prompt: • CoT: “Answer the question thinking step by step...” • Direct: “Answer the question immediately, without any elaboration...” We denote these modified inputs as qCoT and qdirect, respectively. Consider a fixed transformer layer l in the LM, which comprises a self-attention sublayer and an MLP sublayer, combined via residual connections. We extract activation vectors from the residual stream. In particular, let a(l) t (qp) denote the activation vector at token position t in layer l when processing input qp (with p ∈ {CoT, direct}); these activations depend on the generated text sampled from the LM’s distribution P(· | qp). We define the steering vector for a given question q as the difference between the expected activation vectors over token positions: v(q) = Et a(l)      t (qCoT) − Et a(l)           t (qdirect) .      Finally, we aggregate over questions from D to obtain the overall steering vector: v = Eq∼D      v(q) .      This aggregated vector, once scaled by a coefficient c, is used to steer the LM’s output toward CoT reasoning. In the next section, we describe the specific injection strategies we employ. 2.2 STEERING VECTOR INJECTION To steer the LM, we inject the aggregated steering vector v into the residual stream of layer l, scaling it by a coefficient c. This is accomplished using a PyTorch hook that modifies the output of the transformer layer (i.e., after its self-attention and feed-forward sublayers, including residual connection and layer normalization). In our experiments, this injection perturbs the hidden states passed to subsequent layers, thereby influencing the generated text. We explore two injection strategies: 1. Continuous Injection: The steering vector is injected at every token generation step with a low coefficient c, following the approach of Panickssery et al. (2024). 2. Single Injection: The steering vector is injected once during the processing of the input prompt. Through grid search, we determine optimal hyperparameters for both strategies. For example, with Llama3 8B Instruct we use (l = 16, c = 20) for single injection and (l = 13, c = 1) for continuous injection. For Mistral 7B v0.2 Instruct, we use (l = 15, c = 10) and (l = 10, c = 0.5) for the single and continuous strategies, respectively. The lower coefficients for continuous injection reflect the need to avoid over-steering during repeated applications at each generation step.








