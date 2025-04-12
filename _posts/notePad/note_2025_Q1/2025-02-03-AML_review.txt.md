---
title: "[Note] 25-02-03 AML_review.txt"
categories: [Notepad, Daily]
tags: 
date: 2025-02-03 21:49:00 +0900
comments: false
---
---

Anti-Money Laundering by Group-Aware Deep Graph Learning

- AML은 고전적인 데이터 마이닝 문제이다.
- 사용자 신원 및 금융 행동 데이터 = 자금세탁 거래를 탐지에 사용되는 데이터
# 고급 빅데이터 기반 분석 기술 // 이쪽으로 공부해야 하나?
- 규칙기반 접근 방식은 쉽게 우회될 수 있으며 새로운 유형의 자금세탁 범죄를 탐지하는 데 적합하지 않음
- 그러나 자금 세탁범들이 새로운 수법을 개발하고 조직적인 범죄를 저지르는 경우가 많다는 점을 고려할 때, AML 알고리즘의 실제 성능은 적응성과 일반화 능력에 달려 있습니다. 
	---- 단순 성능도, 해석 가능성도 아닌, 일반화 능력(적응성)에 주안점을 둠★★★흠 맞는말이기도 해
- Deep Graph Representation Learning : 딥러닝을 사용하여 그래프의 구조와 특성을 표현하는 기술

_________________________________________________________________________

##데이터 뭐 썼나
- 전 세계에서 가장 큰 은행 카드 제휴 중 하나의 실제 데이터 세트 (*실증 연구)
- 실측값 라벨은 은행 카드 사용자가 보고하고 협력 금융 기관의 금융 도메인 전문가가 확인

##다루는 문제점이 머냐
- 범죄 조직이 조직적으로 수행하는 자금세탁 활동이 증가하고 있지만, 기존 연구들은 여전히 ​​각 계정의 동작을 독립적인 신원 행동으로 취급함. 
- 그룹 수준의 공모된 상호 작용을 고려하지 않음!

##접근법 (알고리즘, 자료구조, etc)
- 조직적 자금세탁 탐지를 위한 Group-Aware Deep Graph Learning 기반 접근 방식
- 사용자 거래 그래프의 노드와 속성을 표현하고 인접한 갱단의 행동을 도출하기 위해 community-centric encoder를 설계
- 유사한 거래 feature를 가진 노드를 수용하기 위한 scheme of local enhancement(로컬 향상 방식?)
- which are aggregated into gangs for downstream detection (이를 다운스트림 탐지를 위해 갱으로 집계??)


##모델 아키텍처 소상히 리뷰
### Group-Aware Deep Graph Learning
### community-centric encoder
### scheme of local enhancement
### downstream detection (aggregated into gangs)


##비교한 기존 벤치마크
- 비교를 이렇게 많이 했다고;



##결과
- 오프라인 및 온라인 모드 모두에서 최첨단 방법을 능가한다는 것
- 개별적, 조직적 자금세탁 거래에서 모두 우수한 성능


_________________________________________________________________________Explain in details

---------1. Intro
- 자금세탁의 심각성 (매우 중요한 금융 범죄 문제)

- 금융산업의 기존 솔루션 방식 - 입법적 고려 사항과 규정 준수 요구 사항에 초점을 맞춤
시중에 나와 있는 대부분의 AML 방식 - 규칙 기반, ML 학습 기반 확률 추론, 
자금 세탁 행동 패턴을 탐지하는 데 상당한 어려움을 겪고 있음? 이유는, 매개변수 용량이 매우 제한적?

- (기존 딥러닝 기반 모델) 컨볼루션 특징 학습(22) [13], 시퀀스 학습(20) [14], 그래프 패턴 학습(21) [15] 등 
	방대한 거래 행동 데이터를 활용할 수 있는 다양한 딥러닝 기반 모델이 자금 세탁 탐지에 활용
[16] ('18) 결정 트리를 stacked auto-encoder (SAE) and restricted Boltzmann machine (RBM)과 결합한 앙상블 방법
[17] ('18) Fast-Graph Convolutional Networks (FGCN)으로 AML을 위한 거래의 그래프 표현을 학습

- 이러한 접근 방식이 주목할 만한 성공을 거두었음에도 불구하고, 
우리는 또한 기존 방법이 그룹 수준(갱단 수준) 상호 작용을 고려하지 않고 각 신원을 독립적인 계정으로 취급한다는 것을 관찰했습니다.
	// 흠, 이런 포인트를 찾아야 하나???
동맹 범죄자는 종종 여러 계정을 조작하여 다양한 채널로 많은 양의 돈을 분산시키고 이러한 거래를 """지나치게 일관된 그룹 패턴"""으로 도출합니다.
[그림 2] 에 표시된 것처럼 우리의 직감은 실제 데이터 세트에서도 증명됩니다.
	먼저 사용자 거래 그래프를 구성합니다.
	여기서 범죄 노드를 빨간색으로 표시합니다.
	보시다시피, 범죄 패턴은 그룹 수준 구조에서 분명합니다. 
이러한 관찰에서 영감을 받아 그래프에서 구성된 """계정 간의 그룹 상호 작용"""이 자금 세탁 거래를 감지하는 데 필수적일 수 있다고 생각합니다.
	// 의문1: 라벨링 어떻게 함??
	// 의문2: 이 그래프 표현은 어떤식으로 한거임? 뭉쳐있는 기준 같은거

- we devise a Group-Aware (gang-aware) Graph neural network-based approach (GAGNN) 
위상 및 속성별 정보를 모두 사용하여 그래프를 인코딩하기 위해 커뮤니티 중심 인코더를 설계
	// 커뮤니티 중심 인코더라
유사한 거래 특징을 가진 노드를 수용하기 위해 로컬 향상 계층의 체계를 고안하여 그룹으로 집계하여 조직적 행동 패턴을 학습
	// what>???
의심스러운 확률을 추론하기 위해 공동 최적화 전략(joint optimization strategy)을 채택
	// 확률 추론 전략 = joint optimization strategy



---------2. Preliminaries
- 본 논문에서, 그룹 인식 그래프 신경망을 사용하여 데이터 기반 자금 세탁 방지에 대한 관찰과 결과를 보고한다고 함
	이 섹션에서 데이터 기반 분석을 통해 사용자 거래 그래프에 대한 관찰 결과를 보고함
- 여러 그룹으로 묶여 있는 클러스터형 거래 패턴이 있는데, 
	이 클러스터는 모두 정상적인 사용자 노드일 수도 있고 대부분 자금 세탁에 연루된 노드일 수도 있음
	// 그러니까, 일반적인 사용자-거래 그래프로 표현해봤더니, 이런 클러스터형 거래 패턴이 보이더라? 이걸 그룹으로 간주해보자?
	// 그룹 간의 모호한 연결? 기존 방식은 그룹 수준의 거래행동을 표현하지 못한다? 흠 흠흠
 이런 흐름에 따라, ML 탐지 프로세스에 """그래프 분석 접근 방식"""을 사용해 보자.
- 그러나, 인접 노드로 특징을 추론하는 그래프 학습 방법을 단순히 사용하는건 하책임!
	범죄자와 일반 사용자 간에 많은 직접적인 연결이 있기 때문	// 이거 이해 안됨. 정상 사용자도 많이 인접해 있으니까 무쓸모란 뜻인가?
	(because there are also many direct connections between criminals and normal users)
따라서 조직 범죄 조직에서 적절한 학습을 ​​할 수 있다면 AML 접근 방식의 성능을 크게 개선할 수 있습니다.



---------3. The Proposed Method (아키텍처)	// 소상히 분석!!!
---------A. Architecture Overview
1) 커뮤니티 중심 인코더 (Community-centric encoder)
- transaction record를 그래프로 변환: 위상 및 속성(topological and attribute-wise) 정보를 모두 사용해 노드를 인코딩	// 노드를 인코딩한다라?
- 특히 그래프 표현 학습을 적용해, 에지 특징에서 노드 특징을 생성한 다음, 컨볼루션 레이어를 활용해 노드의 임베딩을 학습하고,  // ★★★
- 이를 완전히 연결된 레이어에 공급해 노드 분류 결과(predicted label) 생성	// 여기서 바로 라벨을 만듣다? 바로 분류 해버려?

2) 그룹 표현 레이어 (Group representation layer)
- 새로운 에지 표현을 생성하고 얕은 신경망으로 훈련하여 거래를 분류함
- Community-centric encoder가 자금 세탁으로 추론한 거래와 연결된 노드를 "그룹으로 병합하는" group aggregation strategy 도입! // 그냥, 인접한 노드를 그룹으로 병합하는 건가?
- 이후 새로운 그룹 사용자 트랜잭션 그래프를 도출하고, 이를 커뮤니티 중심 인코더에 제공하여 그룹 표현을 추론합니다. 

3) 예측 네트워크 (Prediction network)
- 예측 네트워크는 Node classification loss, Transaction classification loss, and Group detection loss을 결합하여 정의되는 joint optimization strategy 적용
- 사용자 트랜잭션 그래프는 완전 연결 그래프는 아니며, the type of prediction task is inductive (귀납적이다??)


---------B. 커뮤니티 중심 인코더★★★
- transaction을 사용자 트랜잭션 그래프 G=(V,E)로 구성
- (피처 엔지니어링) 금액, 횟수 등의 기본 트랜잭션 속성을 포함해 총 k개의 피처를 구축
- 네트워크 구조를 더 잘 활용하기 위해 에지 단위의 시맨틱 정보 대신 노드 단위의 정보를 네트워크에 공급
	// 엣지 단위(transaction) 정보는 지우고, 노드 단위(account) 정보만 살린다? 지울 필요는 업자나
- 심층 그래프 표현 학습(deep graph representation learning)을 도입하여, 노드에 대한 속성 행렬 X를 도출하고 노드 V_i의 속성 벡터는 다음과 같이 정의
	// Deep Graph Representation Learning = 노드에 대한 attribution Matrix 도출?
- 노드 vi의 속성 벡터는 연결된 에지에서 집계되며, 노드 vi의 라벨은 노드 vi가 이전 ML 거래에 관여했는지 여부를 나타냄
	// Mi = 노드 Vi와 연결된 엣지의 인덱스
	// ej = j번째 거래의 feature vector
	// yj = j번째 거래의 ground-truth label
	// yj_hat = suspicious money laundering label (?)

그래프 학습(graph learning)에 대한 최근 연구는 convolution, recursion, attention 메커니즘을 GNN에 도입함
- 컴퓨터 비전 분야에서 뛰어난 성능을 보인 convolutional networks에서 영감을 받아, 다양한 graph convolution 방법이 등장함
- 스펙트럼 기반 그래프 컨볼루션 네트워크 (Spectral-based graph convolution networks)는 전체 그래프를 동시에 처리해야 해서 큰 그래프로 확장이 어려움.
- 그래서 공간 기반 그래프 컨볼루션 네트워크(Spatial-based graph convolution networks)가 급속도로 발전
-- -그룹화된(커뮤니티) 문자를 무시하고 가장 가까운 이웃 노드의 정보를 집계하여 그래프 구조에서 직접 컨볼루션을 수행함 [18] [19]

- 본 논문에서는, community-aware detection을 위해 고안한 graph network 인코더로 마르코프 랜덤 필드(eMRF)를 확장하여 GAT (graph attention layer)을 활용함 ★
	// 그러니까, 본 논문에서 나오는 GN인코더는 community-aware detection(*group-aware랑 다른거임?)을 위해 eMRF로 확장한 GAT를 쓴다?
- 먼저 수학적으로 다음과 같이 표현할 수 있는 두 개의 GAT 레이어를 구축함
	// A(l) = l번째 GAT layer의 attention matrix
	// X(1) = input node attribute matrix
	// α(l) = l번째 GAT layer의 attention weight
	// zi(l) = l번째 GAT layer의 linear transformation에 대한 output vector
	// H(0), H(1) = 모델에서 학습할 매개변수
- 네트워크에 topological information과, semantic information를 학습할 수 있도록 하는 attribute matrix을 포함시키기 위해, 행렬 A를 변환했음
	// 이 해석이 맞나 모르겠네

- 수식3까지 하면 모델은 노드를 분류할 수 있는 결과 행렬 X(2)를 얻음
- 근데 GAT는 group-aware neighboring nodes를 강화하기 위한 smoothness constraint가 없기 때문에, 상대적으로 거친 분류 결과(coarse classification result)만 얻을 수 있음
- 그래서, graph attention layer를 추가한 extended Malkov random fields (eMRF)를 소개하겠다★
	마르코프 랜덤 필드의 핵심은 energy function이며, 이는 단항 포텐셜 함수와 쌍방향 포텐셜 함수로 구성됨 (unary potential function & pairwise potential function)
	마르코프 랜덤 필드 모델은 graph networks의 "one shot, all shot" 특성을 시뮬레이션하고 단순화함으로써 
	unary potential function가 그래프 컨볼루션 네트워크에서 노드 분류 확률을 구하고 노드 분류 결과를 측정함
	pairwise potential function은 노드 간의 관계를 설명하는 데 사용되며, 유사한 노드에는 동일한 레이블이 할당되고 더 많이 다른 노드에는 다른 레이블이 할당되도록 유도함

-  eMRF 레이어는 GAT의 대략적인 분류 결과를 입력으로 받아, 노드를 서로 다른 커뮤니티로 간주하여 네트워크가 동일한 커뮤니티에 있는 노드에 유사한 레이블을 할당하도록 유도함 
- 따라서 eMRF 레이어는 노드 간의 고유한 커뮤니티 관계를 마이닝하여 원활한 분류를 생성함
- 즉, pairwise potential function의 계산을 완료하기 위해서는 노드의 유사도를 정의해야 하는데, 두 노드 간의 유사도가 높을수록 두 노드 사이에 작용하는 힘이 커져 두 노드에 더 가까운 레이블이 할당되도록 함
- 노드 간의 topological and attribute similarity를 모두 측정하기 위해 MRF의 unary potentials and the pairwise potentials를 GCN으로 수정함
- 따라서 노드 간의 토폴로지 유사성과 속성 유사성을 모두 측정하기 위해 eMRF에서 단항 전위와 쌍 전위를 제안합니다?
- γ(vi,vj)는 노드 vi와 vj 사이의 속성 유사성을 측정하기 위해 다음과 같이 정의됩니다?
	// β = 토폴로지와 속성의 균형을 맞추는 트레이드오프 매개변수
	// ϵ(vi,vj) = 어쩌구 입니다
	// ζ(vi,vj) = 노드 vi와 vj의 속성 간의 cosine similarity [제타]
	// Ri = 모든 ζ(vi,vj) 쌍에 정규화를 적용
- proposed pairwise potentials = Ψ(vi,vj) = −1^σ(vi,vj) * γ(vi,vj)

// !~~~~~설명 잠시 킵~~~~! --수식(6,7)

- 다시 말하자면, GCN 인코더는 토폴로지 및 속성 기능을 통합하는 역할임
- 본 논문에서는 community-centric encoder가 노드의 임베딩을 생성한 후 fully connected layers가 분류기 역할을 함
- 실제로 다음과 같이 표현할 수 있는 얕은 multi-layer perception (MLP) network를 채택했음 -- 수식(8)
- 마지막으로, 노드를 분류하는 인코더를 기반으로 cross-entropy를 사용하여 node classification loss에 도달함 -- 수식(9)



// C 들어가기 전에, 백그라운드 공부하고, B 다시 보고, 그 다음에 representation 단계로 넘어가
// 아 노션 꺼내~!!!
---------C. 그룹 표현 계층


---------D. 예측 네트워크


---------E. 복잡성 분석 및 구현









---------5. Related Work,가 이렇게 뒤에 있다고???
금융 네트워크에서의 자금세탁 탐지와 그래프 학습에 관한 최신 문헌에 대한 검토

---------A. 자금세탁 탐지 
의사 결정 트리, SVM, 랜덤 포레스트와 같은 지도 학습과 클러스터링과 같은 비지도 학습으로 분류
- Wang 등[39]은 금융 데이터를 거래 그룹으로 그룹화하기 위해 CLOPE(Clustering With Slope)를 구현함
- 클러스터링 접근 방식은 거래의 흐름과 계정 간의 관계를 무시함

비지도 데이터에 대한 베이지안 네트워크로 확률론적 그래픽 모델링 기법(PGM)을 제안 [40]
랜덤 포레스트와 서퍼 벡터 머신을 사용하여 AML을 위한 지도 학습을 구현 [41]

학습 접근법(Learning Approaches)
모델을 선택하려면 정확도와 해석 사이의 절충이 필요합니다[38], [42]		// 홀리 여기서도 해석이
- 블랙박스 모델은 종종 매우 정확한 결과를 제공하지만 상대적으로 해석 능력이 부족하기 때문에 
	딥러닝 기반 데이터 마이닝 모델은 금융 기관에 모델 설계의 근거를 설명하기 어려우며 비용 또한 비쌈 [11], [43]
- 의사결정 트리와 선형 회귀[44], [45]와 같은 화이트박스 모델은 해석 가능성이 강하고 모델을 설명하는 데 비용이 적게 들지만 
	일반적으로 결과의 정확도가 낮고 성능이 금융 기준을 충족하지 못함

그러나 자금 세탁범들이 새로운 수법을 개발하고 조직적인 범죄를 저지르는 경우가 많다는 점을 고려할 때, AML 알고리즘의 실제 성능은 적응성과 일반화 능력에 달려 있습니다. 


---------B. 그래프 학습 
- [59]은 그래프 신경망으로 시간 및 위치 기반 그래프 특징을 학습하는 신용 카드 사기 탐지를 위한 공간-시간 주의 기반 그래프 네트워크(STAGN)를 제안
	// 정보 추가
- Wang 등[60]은 사기 탐지를 위해 레이블이 지정된 데이터와 레이블이 지정되지 않은 데이터를 동시에 활용하는 준지도 주의적 그래프 신경망을 제안
	// 레이블링 데이터 관점

그래프 신경망의 발전과 함께 그래프 기반 이상 거래 탐지 방법[61], [62], [63], [64]이 괄목할 만한 발전을 이루었다

[64]은 GNN 기반 메시지 전달 프레임워크를 통합하여 로컬 이상치 탐지 문제를 해결
(***요약: 그룹 단위 관점의 연구는 없었다)


그룹 인식 이상 탐지에 대한 기존 모델들은 
계층적 베이즈 모델 [69], [70], 커널 기반 모델 [71], 심층 생성 모델 [67], 자동 인코더 [72] 등 다양한 기법을 사용하여 
표 형식 및/또는 순차적 데이터 [66], [67], [68]에 대해 연구


이상 징후 탐지 분야에서 가장 유사한 작업은 그래프에서 비정상 패턴 [73], [74], 커뮤니티 [75], [76] 및 하위 그래프 탐지 [77], [78], [79]

1) 주석이 달린 레이블과 레이블 지도 프로세스는 모델 학습에 필수적입니다. 따라서 자금 세탁 탐지 문제를 지도 작업으로 공식화했습니다. 이상 징후 탐지 방법은 라벨링된 정보를 적절히 사용하지 못해 결과적으로 최적의 성능을 발휘하지 못할 수 있으며, 2) 의심스러운 행동은 그 특성상 점점 더 숨겨지고 있습니다. 이러한 패턴은 라벨링된 데이터 뒤에 숨어 있기 때문에 일반적인 이상 징후 탐지 접근 방식을 사용하면 이 문제를 처리하기 어렵습니다.
// 잉??? 이게 무슨 주장이야

거대하고 조직적인 행위, 복잡한 그래프 구조로 변화하는 자금 세탁 패턴
---> AML의 문제. 후자를 위해 하위 그래프 탐지 연구가 진행되고 있고, 전자를 위해 본 논문에서 그룹 단위 연구에 들어감★


연구자들은 최근 의심스러운 거래를 검색하기 위해 고성능 그래프 분석 방법을 도입했습니다[9], [84]. 하지만 이 방법은 모델 학습에서 그래프 특징을 유추할 수 없습니다. 


// 오 암호화폐 얘기 나왔음 (***낯설면 안되겠지?)★★★★★★
- 최근에는 그래프 학습 기반의 의심 거래 탐지 방법[85], [86], [87]이 자금 세탁 탐지에 활용되고 있습니다. 
- 예를 들어, Weber 등[17]은 금융 데이터의 포렌식 분석에 그래프 컨볼루션 신경망을 사용하며 
	AML을 위한 그래프 딥러닝이 범죄 금융 활동과의 싸움에서 큰 잠재력을 가지고 있다고 추론합니다. 
- 알라랍 등[88]은 블록체인 네트워크에서 자금세탁 방지를 위한 그래프 기반 장단기 메모리(LSTM) 모델을 제안했습니다. 
- 암호화폐 문헌에서 그래프 기법은 자금 세탁 탐지에 널리 활용되고 있으며[89], [90], [91], 
거래 수준 그래프에서 학습하는 것이 더 우수하다는 것을 보여줍니다[92].


## 그래프 학습 기반의 의심 거래 탐지 방법★
[85] “Smurf-based anti-money laundering in time-evolving transaction networks,” ['21]
[86] “Improving anti-money laundering in bitcoin using evolving graph convolutions and deep neural decision forest,” ['22]
[87] “Temporal graph learning for ﬁnancial world: Algorithms, scalability, explainability & fairness,” ['22]

##  그래프 컨볼루션 신경망★
[17] “Scalable graph learning for anti-money laundering: A ﬁrst look,” ['18]

## 그래프 기반 장단기 메모리(LSTM) 모델★
[88] “Graph-based LSTM for anti-money laun- dering: Experimenting temporal graph convolutional network with bitcoin data,” 2023.

## 암호화폐 문헌에서 그래프 기법★
[89] “Inspection-L: self-supervised GNN node embeddings for money laundering detection in bitcoin,” 2022  // 눈에 익다. 인용 많이 됐네. Self-Supervised GNN Node Embedding
[90] “Eigenvector-based graph neural network embeddings and trust rating prediction in bitcoin networks,” 2022  // 고유벡터 기반????? 흥미롭네
[91] “On the potential of a graph attention network in money laundering detection,” 2022.	// GAN에 대하여

## 거래 수준 그래프에서 학습하는 것이 더 우수★
[92] “Anti-money laundering in bitcoin: Experimenting with graph convolutional networks for ﬁnancial forensics,” 2019





###이 논문에서 GN인코더를 위해 참고한 논문 2개
---- Community-centric graph convolutional network for unsu- pervised community detection [IJCAI '21] ★
	// 커뮤니티(그룹) 찾기가 본질적으로 비지도 학습 문제임에도 불구하고 기존 연구는 반지도 방식임에서 출발	// 어떤 분류의 문제인지 파악
	// Autoencoder 프레임워크 하에서 비지도 커뮤니티 감지를 위한 새로운 GCN 접근 방식을 개발
---- Intention-aware heterogeneous graph attention networks for fraud transactions detection [ACM SIGKDD '21] ★
	// 행동 시퀀스에서 사용자 의도를 모델링? 이기종 거래 의도 네트워크
	// IHGAT(Intention-aware Heterogeneous Graph ATtention networks)라는 그래프 신경 방식을 제안
	// 시퀀스와 같은 의도를 인식할 뿐만 아니라 거래 간의 관계도 인코딩한다고