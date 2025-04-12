---
title: "[Note] 25-01-01 paper list.txt"
categories: [Notepad, Daily]
tags: 
date: 2025-01-01 21:49:00 +0900
comments: false
---
---

- 좀더 본질적인 부분을 연구해야 하는데
- 전문가의 의사 결정 능력을 에뮬레이트하는 게 전문가 시스템 (expert system)
- 화이트 박스 모델 = 모델의 내부 구조와 의사 결정 프로세스가 투명하고 해석 가능한거
	- 모델 자체가 이러면서 성능을 챙기기 쉽지 않기 때문에, 블랙박스 모델에 XAI를 attach 하는 post-hoc으로 하는거 같은데



## AI / Explainable AI for cybersecurity
- Explainable AI for cybersecurity automation, intelligence and trustworthiness in digital twin: Methods, taxonomy, challenges and prospects [ICT Express ‘24]
	- 사이버 보안 맥락에서의 AI 및 XAI 방법 분류
	# machine learning, deep learning, data-driven, rule discovery, semantic knowledge, multimodal intelligence
	4.1.1. Machine learning
	4.1.2. Neural networks and deep learning
	4.1.3. Rule mining and expert system modeling
	4.1.4. LLM and multimodal intelligence modeling	[82] [9]
		- 멀티모달=다양한 유형의 데이터: 블록체인 + 네트워크 트래픽 데이터 + 맥락적인게 들어가면 LLM으로 넘어가는데, NLP 기술 같은거
	4.1.5. Semantic knowledge representation and reasoning
		-도메인별 지식을 인코딩
		- semantic technology: 
			- ontology(특정 도메인 내 지식의 형식적 표현)
			- knowledge graph(엔터티, 속성, 엔터티 간 관계를 포착하는 지식의 구조적 표현)
		= semantic knowledge graph (의미적 지식 그래프) // 흥미로운데
	4.1.6. Uncertainty modeling (불확실성 모델링)
		AI의 불확실성 모델링은 데이터 불확실성과 모델 불확실성의 두 가지 주요 차원을 포함합니다. 데이터 불확실성은 노이즈, 편향, 누락된 값, 이상치, 불완전성 및 실제 현상의 변동성과 같은 사용 가능한 데이터의 품질, 양 및 대표성의 제한 으로 인해 발생합니다. 반면 모델 불확실성은 아키텍처 복잡성, 매개변수 추정 및 일반화 기능으로 인해 알고리즘에 내재된 가정 및 제한을 말합니다. 데이터 불확실성은 종종 강력한 전처리, 데이터 증강 또는 통계적 방법을 통해 해결할 수 있지만 모델 불확실성에는 종종 보다 정교한 접근 방식이 필요합니다. 확률적 그래픽 모델 , 퍼지 논리, 신념 함수 , 베이지안 추론 또는 몬테카를로 방법을  포함하여 이러한 다양한 유형의 불확실성을 해결하기 위해 다양한 기술이 사용됩니다 [87] , [88] . 베이지안 네트워크 와 같은 확률적 그래픽 표현을 사용하는 모델은 불확실성을 표현하고 추론하기 위한 구조화된 프레임워크를 제공하는 반면, 퍼지 논리는 부정확하거나 정성적인 데이터로 작업할 때 유연성을 제공합니다. 반대로, 앙상블 학습 , 드롭아웃 정규화 , 모델 보정, 베이지안 모델 평균화, 민감도 분석  [89] 과 같은 기술을 통해 모델 불확실성을 완화할 수 있습니다 . 이는 다양한 가정과 매개변수에서 AI 모델의 예측이 얼마나 강력한지 평가합니다. AI 시스템에서 견고성과 신뢰성을 보장하려면 불확실성 모델링의 이 두 가지 측면을 균형 있게 조절하는 것이 중요합니다.

	4.2 . 사이버 보안 모델링을 위한 XAI 방법
		모델의 결정을 설명하기 위해 이미지나 플롯을 생성하는 히트맵, 차트, 그래프 등과 같은 시각적 설명 기술이 포함될 수 있습니다.

	6. 잠재적인 연구 방향을 포함한 과제 및 미래 전망
		- data augmentation techniques through generative AI such as generative adversarial networks (GANs) or variational autoencoders (VAEs)
		- 정확도가 높지만 여러 거짓 양성을 생성하는 모델은 실제 사이버 사용에 실용적이지 않을 수 있습니다.
		- 확장 가능한 규칙 발견 알고리즘을 개발하고 결국 이에 따라 동적 의사 결정 (자동 규칙 생성)
		- AI 모델 해석 가능성 및 신뢰성 (AI Model Interpretability and Trustworthiness)
			-  AI 출력을 설명하고 "불확실성을 정량화"하며 인간 피드백을 통합하는 기술을 개발하는 것
		- 자동화, 인텔리전스 및 신뢰할 수 있는 의사 결정을 지원하는 견고한 AI 프레임워크
			- 시스템의 신뢰성과 효과를 보장하기 위해 다양한 데이터, 시뮬레이션된 공격 시나리오, 실제 테스트를 포함한 엄격한 실험적 평가 필요

- Detecting anomalous cryptocurrency transactions: An AML/CFT application of machine learning-based forensics [‘23] ☆☆☆
	- 그래프 합성 네트워크(GCN) 및 그래프 어텐션 네트워크(GAT)가 유망한 AML/CFT 솔루션임을 보여줌 

## XAI for Cyber Forensic
- XAI-CF -- Examining the Role of Explainable Artificial Intelligence in Cyber Forensics [arxiv ‘24]
	- 여기서 가져갈게 많았음. 정리해놔



## Cryptocurrency Forensics





## Explainability in GNN
- Explainability in Graph Neural Networks: A Taxonomic Survey [IEEE Transactions on Pattern Analysis and Machine Intelligence '22] ★★★




##  XAI (https://xai.kaist.ac.kr/Tutorial/2020/)
# Relevance Score based
- Relative Attributing Propagation: Interpreting the Comparative Contributions of Individual Units in Deep Neural Networks. [AAAI '20] ★

# Explaining Internal Nodes
- An Efficient Explorative Sampling Considering the Generative Boundaries of Deep Generative Neural Networks. In Thirty-Third [AAAI '20] ★

# Explainable Actions
- Monte-Carlo Tree Search in Continuous Action Spaces with Value Gradients. [AAAI '20] ★

// https://xai.kaist.ac.kr/Tutorial/2024/



## XAI for illicit activity Investigation
- Large Language Model XAI approach for illicit activity Investigation in Bitcoin ['24] ☆☆☆


☆☆☆ = 분야가 딱 맞는 논문
★★★ = 그냥 좋은 논문 (탑컨퍼런스)

----------------------------------------------------------------------------------------
// 이번주 랩미팅은 위 논문들 핵심만 리뷰하면서 밑에 내용을 말씀드리는 방향으로?
// 그리고 퍼징 논문들 읽어봐야 되는데


## 내가 하고싶은거
- 법정에서 유효하게 쓰일 수 있는 Fraud Detection AI model
- 성능과 설명가능성을 모두 잡은 AI model
	- 성능을 보이는 "그 과정 자체 또는 그 결과물"에 대한 재분석이
	  이해가는 논리적인 설명으로 이어져야 함


## 문제를 정의하자면




## 앞으로 어떤식으로 전개할 거냐면




##더 고려해야 할 사안

- 더 세분화된 타겟
	- 어떤 Fraud를 타겟으로 하는가 
	- 자금세탁이라면 어떤 행위를 말하는가



----------------------------------------------------------------------------------------
## 이렇게 갔을 때 오픈소스 구현은 어떤걸 할거냐
1안) 
2안) 

- 근데, 이 구현의 목적이 뭔지 알고 시작하고 싶은데
	- 흠 냅다 물어봐야겠는데, 너무 애매함


----------------------------------------------------------------------------------------
## 그외 논의거리
- (데이터) 불법 거래 데이터 라벨링 문제
- (공격 관점 고려) 뭘 공격하는거지? 블록체인 시스템? 비트코인 시스템? 비트코인 시장? 거래자? e.g. crypto wash trading?







