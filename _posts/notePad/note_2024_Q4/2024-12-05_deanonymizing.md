---
title: "[Note] 2024-12-05 deanonymizing.txt"
categories: [Notepad, Daily]
tags: 
date: 2024-12-05 21:49:00 +0900
comments: false
---
---

시의성: 트럼프
주목할 점은~


1.
사용자 익명성과 책임성(추적 가능성)에 대한 논의 ---> 이게 최종적인 목표가 돼야하지 않을지?

- 개인정보보호를 위한 익명성 추구와 불법 거래 추적을 위한 투명성? 추적가능성?의 균형

- blockchain 익명성과 관련한 논문들


2.
도메인은 어디로 정할지

- 유동성 관점에서는 비트코인
- 익명성 관점에서는 monero, zcash --> 규제가 빡세지면 이쪽은 사장되지 않을지?
	--> 실제로 뭐가 쓰이는지 분석한 논문이 있었는데 못찾겠네;;



3.
자금세탁 거래 탐지 방법론 (목표를 기준으로 분류하고 싶은데)	// 리뷰 논문들 스르륵, 세부 방법론 설명 추가, 분야 정하고 한계점 체크

## 익명성 해제 (Deanonymizing): 트래픽 분석 관점의 노드 식별
- Deanonymizing Transactions Originating from Monero Tor Hidden Service Nodes [WWW '24]
- SoK: assumptions underlying cryptocurrency deanonymizations (Deuber et al.) (2022)
- Perimeter:Anetworklayer attack on the anonymity of cryptocurrencies [FC '21]
- A Two-Stage Deanonymization Attack Towards Bitcoin Hidden Service Nodes [IEEE HPCC '21]
- Remote {Side-Channel} Attacks on Anonymous Transactions [USENIX '20]



## 믹싱 계좌 식별 (Discovering Bitcoin Mixing, De-mixing Alg.): 믹싱 서비스 관점
	// The Satoshi laundromat: a review on the money laundering open door of Bitcoin mixers [Journal of Financial Crime, '23]
- LSTM-TC: Bitcoin coin mixing detection method with a high recall [Applied Intelligence '22] : LSTM Transaction Tree Classifier (LSTM-TC)
- Mixing detection on bitcoin transactions using statistical patterns (Shojaeenasab et al.) (2022): 휴리스틱 알고리즘
- Detecting mixing services via mining bitcoin transaction network with hybrid motifs (Wu et al.) (2022)
	: 네트워크 수준, 계정 수준, 거래 수준의 세 가지 수준에서 믹싱 서비스의 통계적 속성을 식별하기 위한 feature-based 네트워크 분석 프레임워크 제안
- Bitcoin mixing detection using deep autoencoder [IEEE DSC '18]: 딥 오토 인코더를 사용한 그래프 임베딩 알고리즘
- A Practical De-mixing Algorithm for Bitcoin Mixing Services [BCC '18] : 고정 수수료 믹서에 대한 믹싱 해제 알고리즘
- Discovering bitcoin mixing using anomaly detection (Prado-Romero et al.) (2018): 휴리스틱 + 그래프 클러스터링



## 거래에 대한 이상 패턴 탐지: 의심스러운 거래 관점

"Detecting Anomalous Cryptocurrency Transactions: An AML/CFT Application of Machine Learning-based Forensics" [2023]
"Probabilistic Sampling-Enhanced Temporal-Spatial GCN: A Scalable Framework for Transaction Anomaly Detection in Ethereum Networks" [2023]
"CTDM: Cryptocurrency Abnormal Transaction Detection Method with Spatio-Temporal and Global Representation" [, 2023]
- 그래프 기반 모델: GCN, GAT와 같은 그래프 신경망(GNN) 활용

"Detecting Anomalies in Blockchain Transactions using Machine Learning Classifiers and Explainability Analysis" [arXiv, 2024]
- 머신러닝과 설명 가능성: 앙상블 방법, 특히 트리 기반 분류기는 설명 가능성 기술(예: SHAP)과 통합
- 데이터 불균형 솔루션: XGBCLUS 및 확률적 샘플링과 같은 고급 샘플링 기술을 사용하여 데이터 불균형 해결
- 시공간 분석





Jason Hirshman, Yifei Huang, and Stephen Macke. 2013. Unsupervised approaches to detecting anomalous behavior in the bitcoin transaction network. 3rd ed. Technical report, Stanford University (2013).

Thai Pham and Steven Lee. 2016. Anomaly detection in the bitcoin system-a network perspective. arXiv preprint arXiv:1611.03942 (2016).

Patrick M Monamo, Vukosi Marivate, and Bhesipho Twala. 2016. A multifaceted approach to bitcoin fraud detection: Global and local outliers. In 2016 15th IEEE International Conference on Machine Learning and Applications (ICMLA). IEEE, 188–194.



4.
새로운 암호화폐를 제안할 수 도 있음

- 익명성과 추적 가능성을 갖춘? 이게 Zcash일려나? 한번 봐야겠네
- Traceable Monero: Anonymous Cryptocurrency with Enhanced Accountability [IEEE Transactions '19]




5.
원래 생각했던 잠재적인 연구 방향

- 현업 기술 대비 개선 방향 모색 (실시간성, 적응성, XAI, 라벨링데이터 등)  --> 
- 특정 자금세탁 수법에 특화된 탐지 알고리즘 개발  --> 새로운 자금세탁 기술, 규제 변화 및 분석 방법 서치
- AML 분야에 영지식 증명이 결합될 수 있을지





