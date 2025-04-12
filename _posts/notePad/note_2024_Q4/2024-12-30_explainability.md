---
title: "[Note] 2024-12-30 explainability.txt"
categories: [Notepad, Daily]
tags: 
date: 2024-12-30 21:49:00 +0900
comments: false
---
---


- 법의학 분석을 위한 실용적이고 해석 가능한 포렌식 분석 도구 개발 (for. 금융 기관, 법 집행 기관, 규제 기관)
- 금융 범죄 전술에 대한 적응성
- de-anonymization attacks
-----------------------------------------------------------------------------------------------------------

## AML/CFT와 GNN

# Detecting anomalous cryptocurrency transactions: An AML/CFT application of machine learning-based forensics [Electronic Markets '23]
// https://github.com/fmerizzi/GCN_detect_bitcoin_money_laundering ●

// 백그라운드 설명이 마음에 드는걸
// 전체적으로 내가 하고싶은거랑 너무 비슷한데

- 비트코인의 가명성과 AML/CFT와 포렌식 간의 관계에 대한 통찰력에 대한 개념적 배경
- AML/CFT 프레임워크: 금융 시스템의 무결성을 보호하는 것을 목표로 하는 일련의 법률, 규정 및 절차로 구성 (Pocher & Zichichi, 2022 ) ★
- 불법 주소에서 발생하는 대부분의 가치는 범죄 목적으로 구축된 소수의 서비스로 전송되는 것으로 보입니다(Chainalysis Team, 2023 )

- 우리의 중심 가설에 따르면 자금 세탁은 그래프 구조를 만드는 개체 간 거래 흐름 관계를 수반하므로 
AML/CFT 분석은 머신 러닝의 새로운 그래프 분석 기술, 즉 그래프 합성곱 네트워크(GCN) 및 그래프 주의 네트워크(GAT)로부터 이점을 얻을 수 있습니다.
- As per our central hypothesis, since money laundering involves transaction flow relationships between entities creating a graph structure, 
AML/CFT analytics could benefit from novel graph analysis techniques in machine learning, namely Graph Convolutional Networks (GCN) and Graph Attention Networks (GAT).


- 머신 러닝 및 거래 그래프 분석과 이들의 조합을 기반으로 하는 기술을 실험하는 것의 가치를 강조합니다. 
	- 오 (Weber et al., 2019 )에서 ML이 짱이더라 하는 실험 결론을 보여줬는데, 이를 반박해보겠다.
- 암호화폐 거래 데이터의 양과 복잡성, AML/CFT 이상 지표의 특성과 관련하여 주장을 맥락화 함
- 규칙 기반 체제의 단점, 설명 가능성 측면, 학제간 방법론(interdisciplinary methodology)에 기반한 연구에 참여할 필요성을 고려하였음


"Research into the regulatory impacts of the explainability and interpretability of AI applications is vast and detailed; in light of the scope of this work, we perform inevitable simplifications."
--- 헉 광범위한 작업이였어?


Against this backdrop, forensic methods provide a wide range of information that emerges as pivotal for investigation, compliance, and supervision.
Their value is displayed by the debate on the crypto travel rule, pursuant to which regulated entities must identify originators and recipients of cryptotransfers to guarantee traceability.


암호화 지갑에서 주고받는 거래를 검토하여 합법적이거나 불법적인 거래를 분류합니다(Weber et al., 2019 ). 
원칙적으로 이러한 도구는 주소와 거래를 실제 신원에 직접 연결하려고 하지 않습니다.


Yin et al. ( 2019 )은 아직 식별되지 않은 엔터티 유형을 예측하기 위해 비트코인 ​​블록체인의 익명성을 해제하는 지도 학습 기반 접근 방식을 제시


GuiltyWalker(Oliveira et al., 2021 )는 암호화폐 거래 그래프에서 무작위 움직임을 활용하여 이전의 의심스러운 활동과의 거리를 특성화합니다.


-  GCN의 업데이트된 구현을 사용 ---> 이런 업데이트 구현을 원하시는거겠지??

- 거래 집합과 그 내재적 특성, 즉 암호화폐를 사용하려면 사용자가 이전 거래에서 받아야 한다는 사실을 고려 ★


적절한 거래 분류를 알려줄 수 있는 기준을 식별해야 할 필요성이 발생합니다. 
예를 들어, 거래가 불법인 경우 이웃 거래도 불법인지 또는 그래프 관련 패턴이 의심스러운 활동을 나타내는지 여부를 정의하는 것입니다. 
이러한 문제에 대처하려면 RegTech 분야의 이상 탐지 접근 방식에 대한 명확한 이해가 필수적입니다.


 Indeed, compliance decisions must be explainable and traceable for auditing. 
For this reason, the preliminary review of a flagged account relies on suspiciousness heuristics 
(e.g., political exposure, geographic location, transaction type, users’ behavior) (Weber et al., 2018).

--- 규칙 기반 시스템의 단순성은 해석 가능성을 보장함에도 불구하고 약 95~98%의 거짓 긍정을 추정합니다(Eddin et al., 2021 ). // 띠용 너무 높은데


방대한 양의 데이터를 활용하여 자동으로 학습하고 개선할 수 있는 도구를 제공합니다(Kamiˇsali´c et al., 2021 )


-----------------------------------------------------------------------------------------------------------

## 포렌식 (법적 핵심 요구 사항)

- 여기서 문제가 되는 부분이 "확률론적" 과정에서 기인하는건지?
- 그럼 현재의 XAI 기술로는 해결이 안되는건지?
----- 정확한 문제점 파악 필요★★★

- 고려해야하는 AML 특성이 뭔지?



# SoK: The Next Phase of Identifying Illicit Activity in Bitcoin [ICBC ’23]
- 결과의 진위성을 보장하는 재현성과 검증 가능한 결과
--- 이거 출처가 [FSI '20] 저거네

# Safeguarding the evidential value of forensic cryptocurrency investigations [Forensic Science International ‘20]
 -국제적으로 인정된 표준과 규칙을 식별하여 이를 현재 암호화폐 포렌식 관행에 투영


# Forensic Analysis and Detection of Illicit Transactions in Bitcoin Network [ICOSEC '24] // https://github.com/fiyanmehfil/ForenChain ●
- Forensic Analysis in Cryptocurrencies // 암호화폐의 포렌식 분석에 관한 동향 조사를 잘 해둠
--- 여기 참고해서 나도 정리하기



# XAI-CF -- Examining the Role of Explainable Artificial Intelligence in Cyber Forensics ★★★ 
// 딱 내가 원하는 연구분야인데, 여기서 이걸 XAI-CF라고 명명했음
// 근데 암호화폐 도메인은 아니고, 전반적인 CF을 대상으로 함
// 어쨋든 포렌식 분야에 XAI를 적용하기 위한 논의를 하고있음

- CF에 AI를 성공적으로 적용하려면 AI 시스템에 대한 신뢰를 구축해야 합니다.
- XAI-CF : 사이버 포렌식에 적용되는 설정가능한 XAI 시스템



# Large Language Model XAI Approach for Illicit Activity Investigation in Bitcoin ★★★ 
- 진짜 딱 보고싶은데 못보네

-----------------------------------------------------------------------------------------------------------

## explainability analysis technique	// 컴공적인 내용으로 연구하고싶은데

모델의 설명 가능성에 대한 철저한 분석 수행 
- explainability analysis technique
	- SHAP (SHapley Additive exPlanations)
	-- A Unified Approach to Interpreting Model Predictions [NIPS '17]
	--  SHAP는 게임 이론적 접근 방식에서 모델의 출력 값에 대한 중요도에 따라 각 기능에 대한 값을 설정함

	- LIME (Local Interpretable Model-Agnostic Explanations)

- 어떤 AML적 특징이 이러한 출력에 기여하는지


# Automated Sensor Node Malicious Activity Detection with Explainability Analysis
	--- 그냥 SHAP 사용함


# XAI 연구가 굉장히 많은걸
(Explainable AI for cybersecurity automation, intelligence and trustworthiness in digital twin: Methods, taxonomy, challenges and prospects   '24)
Rawal et al. [27] , 2022	엑스	엑스	엑스	엑스	✓	엑스	✓	엑스	✓	✓	신뢰할 수 있는 XAI의 최근 발전 사항 발표
Ahmed et al. [29] , 2022	엑스	엑스	✓	✓	✓	엑스	*	엑스	✓	✓	산업 4.0에서 AI에서 XAI로의 연구 발표
Ibrahim et al. [13] , 2023	엑스	엑스	엑스	엑스	✓	엑스	✓	엑스	✓	✓	설명 가능한 CNN 중심 XAI 제시
Saeed et al. [30] , 2023	엑스	엑스	엑스	엑스	✓	엑스	✓	엑스	✓	✓	XAI에 대한 체계적인 메타조사를 제시

[13] Explainable convolutional neural networks: A taxonomy, review, and future directions, ACM Comput. Surv., 55 (10) (2023), pp. 1-37
[30] Saeed W., Omlin C., Explainable ai (xai): A systematic meta-survey of current challenges and future opportunities, Knowl.-Based Syst. (2023), Article 110273



## Semantic knowledge representation and reasoning
- 이건 뭐지

-----------------------------------------------------------------------------------------------------------

## 데이터 관련

# BABD: A Bitcoin Address Behavior Dataset for Pattern Analysis [IEEE TRANSACTIONS ON INFORMATION FORENSICS AND SECURITY '24]

- 데이터 세트를 구축하기 위한 프레임워크를 제안
- 서브그래프 생성 알고리즘 제안


# Realistic Synthetic Financial Transactions for Anti-Money Laundering Models [NIPS ‘24]
- 자금세탁 시뮬레이션 데이터 생성



- SMOTE과 결합한 하이브리드 데이터 밸런싱 기술

-----------------------------------------------------------------------------------------------------------
