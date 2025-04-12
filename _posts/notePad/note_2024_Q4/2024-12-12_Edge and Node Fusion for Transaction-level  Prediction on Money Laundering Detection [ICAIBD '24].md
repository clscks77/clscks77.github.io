---
title: "[Note] 2024-12-12 Edge and Node Fusion for Transaction-level  Prediction on Money Laundering Detection [ICAIBD '24].txt"
categories: [Notepad, Daily]
tags: 
date: 2024-12-12 21:49:00 +0900
comments: false
---
---


##Edge and Node Fusion for Transaction-level  Prediction on Money Laundering Detection [ICAIBD '24]
= 노드 레이블과 에지 레이블을 모두 종합적으로 고려하기 위해 트랜잭션 수준 예측을 위한 에지 및 노드 융합 알고리즘을 제안
= 원래 그래프 데이터 구조를 보존하면서 샘플 데이터의 균형을 맞추기 위해 2라운드 모델 학습 방식을 도입
= 결론적으로 자금 세탁 데이터 세트에서 GNN 성능을 현저히 향상시킴

- 불법 거래와 합법적 거래의 공존 ---> 이 포인트는 왜 짚은거지?

### 관련 연구 정리를 잘해줌
- AML 작업에서 GNN 성능 향상  [10]
"Competence of graph convolutional networks for anti-money laundering in Bitcoin blockchain" ['20]

- 이기종 그래프에 맞게 특별히 설계된 AML 알고리즘  [11]
"Finding Money Launderers Using Heterogeneous Graph Neural Networks" ---> unpublished, not cryptocurrency

- 다양한 자금 세탁 패턴을 감지하기 위한 논문 [12] --> 표준 메시지 전달 GNN을 입증 가능한 강력한 방향성 다중 그래프 신경망으로 변환 --> 좋은데★
"Provably Powerful Graph Neural Networks for Directed Multigraphs" [AAAI '24] ★★★

- [13]은 주어진 네트워크 내에서 정보 흐름의 제어를 결정하기 위해 설정된 포화된 중간 중심성 개념을 소개
"Saturated Betweenness Centrality Sets of Vertices in Graphs" [Journal of Advances in Information Technology '21]

- AML 작업에서 불균형 데이터 샘플 문제를 해결하기 위해 [14] 의 저자는 노드의 연결성과 노드 기능의 유사성에 초점을 맞춰 두 가지 그래프 재구성 기술을 제안
"FraudLens: Graph Structural Learning for Bitcoin Illicit Activity Identification"  [ACSAC '23] ★★

- [15] 의 저자는 노드 선택 및 샘플링에 ENN-SMOTE 기술 [16] 을 활용 하고 새로운 노드 간에 에지를 생성하기 위해 어텐션 메커니즘을 소개
 "ESA-GCN: An Enhanced Graph-Based Node Classification Method for Class Imbalance Using ENN-SMOTE Sampling and an Attention Mechanism" [Applied Sciences '24]