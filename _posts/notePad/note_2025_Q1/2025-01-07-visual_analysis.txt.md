---
title: "[Note] 25-01-07 visual_analysis.txt"
categories: [Notepad, Daily]
tags: 
date: 2025-01-07 21:49:00 +0900
comments: false
---
---


* 사이버 범죄 분야의 양상 = 문제 상황 인식
* 그러니까, 사이버 범죄 분야의 양상은 이러한데, 학계에서는 이를 위해 어떤 연구를 하느냐, 어떤 문제를 다룰려고 하느냐, 그 관점으로 집중해보자

* 정의에 따르면, 자금 세탁은 불법 자금을 외부 금융 채널을 통해 유입하여 자금을 합법적인 것처럼 보이게 하는 행위입니다( McDowell, 2001 )
* 일단, 일반 금융거래에서 자금세탁은 다른 국가로 이체하면서 추적을 피함.
	& 다른 금융 관할권으로 이전되어 자금이 압수되지 않도록 보호함


* 데이터를 분류해보면
	- 여러 블록체인을 다루는 "암호화폐 거래소의 거래 데이터" --> 이쪽으로 가면 암호화폐 유형까지 feature로 들어감
	- 단일 암호화폐의 "블록체인 거래 데이터"
		- 애초에 자금세탁은 화폐유형 왔다갔다 하니까 전자를 타겟으로 하는게 맞지
		- 거래소에서 생성된 거래 데이터는 비공개 ---ㄴㅇㄱ그렇구나
	= 암호화폐 거래소에서의 자금세탁 활동을 분석하기 위한 연구


## The use of cryptocurrencies in the money laundering process [Journal of Money Laundering Control '19]
* 배치, 계층화, 통합의 세 가지 기본 단계로 나뉩니다( Gilmour, 2016 ).
	--> [1]에서 2단계가 맞지 않냐며 제안함 --> 납득은 안됨. 굳이 이렇게 나눈다고?싶음

* 돈 세탁에 사용되는 가장 전통적인 방법은 현금 집약적 사업을 전면에 내세워 돈을 예치하는 것입니다. 불법적인 돈은 사업 수익에 포함됩니다. 


## 흥미로운 키워드
* 위험 수준 평가
* 세탁자가 채택한 거래 전략을 추론
* 복잡한 거래 패턴은 무엇으로 이루어져있는가 (시간, 금액, 작업 유형, 암호화폐 종류)
	--> 이를 어떻게 임베딩하면 좋을까
	-----> 데이터 구조로 들어가나? 좋은데?
* 접근 방식

____________________________________________________________________________

[2] Visual Analysis of Money Laundering in Cryptocurrency Exchange [IEEE Transactions on Computational Social Systems '24]
// "암호화폐 거래소를 위한" 새로운 자금 세탁 방지 솔루션 (ML 알고리즘 및 시각화 도구)

(Abstract)
- 연구에서는 암호화폐 거래소와 협력하여 "암호화폐 거래소"의 "자금세탁방지를 위한 새로운 솔루션"을 모색합니다. 
- 거래 감독자의 자금세탁방지 업무에 필요한 데이터 분석 요건 정리
- 이들의 일상 업무를 지원하기 위한 시각적 분석 접근법을 제안
- 의심스러운 자금 세탁 계정을 자동으로 감지하는 새로운 알고리즘



- 실제 거래소의 거래 감독자는, 자금세탁 활동에 연루된 의심 계정을 어떻게 식별하느냐
	- 간단한 규칙을 사용하더라!
	- 그리고 거래 데이터를 수동으로 검증하여 자금세탁의 단서와 증거를 조사하더라!
		(- 그리고 위험 계정을 감시 목록에 추가하거나 법 집행 기관에 직접 보고함)
		(- 자금 세탁 단서를 찾고, 자금 세탁 프로세스를 발견)

- 이때 문제점이 무엇이냐★
	1) (오탐) 위험 수준을 평가하지 않는 무차별적인 규칙 적용이 이루어지고 있더라
	2) (미탐) 자금세탁 의도를 숨기기 위해 규칙적인 빈도와 소액으로 암호화폐 거래를 시작하는 미묘한 자금세탁 활동을 파악하지 못함
	3) (시간) 수동 검증에는 상당한 시간이 소요됨 (수동 검증이 뭘 어떻게 한다는건지 모르겠지만)
* 정확도, 리콜, 정밀도


- 거래 감독자들이 필요하다고 말하는 것
	-(R1) 의심스러운 계좌를 자동으로 감지하는 지능형 알고리즘 & 검증을 위한 해석 가능한 정보 제공
	-(R3) 시간 경과에 따른 계정 잔액 변화 시각화
		-  잔액 변화 패턴은 잠재적인 자금 세탁 활동을 나타낼 수 있다?!
		---> 계정의 잔액이 빠르게 증가한 후 갑자기 잔액이 감소하는 것은 시끄러운 자금 세탁 활동과 관련된 중요한 단서
	- (R4) 자금 세탁은 특정 기간 동안 지속되며, "다양한 금액, 여러 암호화폐, 여러 작업 유형"이 포함된 여러 거래가 포함된 3단계 프로세스임
		- 이에 대한 시간적 변화를 패턴화해야 자금세탁의 단서를 찾을 수 있음



(이 논문에서 제안하는거 2가지)
1) 의심스러운 자금 세탁 계좌 탐지(SMLAD; Suspicious Money Laundering Account Detection) 알고리즘 (for R1)
	- 거래 행동을 특성화하기 위해 40개의 통계적 특징으로 구성된 피처 시스템을 정의 ---> 정량화된 위험도 제공 가능
	- 의심스러운 계정을 감지하기 위해 이상 탐지 방법을 클러스터링 방법과 통합
2) 시각적 분석 인터페이스




## (일반 금융권에서의?) 자금세탁 탐지★
- 자금세탁 과정에는 대규모 블록 또는 고빈도 거래, 휴면 계좌의 재활성화, 새로 개설한 계좌의 즉각적인 폐쇄
   와 같이 정상적인 활동과는 구별되는 복잡한 금융 활동이 포함됨
	* An intelligent anti-money laundering method for detecting risky users in the banking systems ['14]

- 은행, 주식, 보험과 같은 기존 거래소에서 이러한 금융 활동을 탐지하고 추적하여 자금세탁 방지를 위해 많은 방법이 제안됨
	* Anti-money laundering systems: A systematic literature review ['20]
	* Anti-money laundering: Using data visualization to identify suspicious activity ['19]

- Netmap [21] , Anacpapa Chart [22] , XANALYS Link Explorer [23] 와 같은 그래프 기반 접근 방식이 등장하여 자금세탁 방지에 새로운 분야
----- 기존의 자금세탁 방지 접근 방식은 대체로 기존 금융 시스템을 중심으로 하고 있는 반면, 이 연구에서는 암호화폐 거래소의 자금세탁 방지에 초점을 맞춥니다.



## 암호화폐 거래 데이터 분석

- 에어드랍 캔디 및 탐욕스러운 자본 주입과 같은 비정상적인 거래 행동을 식별했습니다 [36] , [37] 
	* “A large-scale empirical analysis of ransomware activities in bitcoin ['22] ---- 뭐야 이거! 봐야겠어
	* “An abnormal transaction detection mechanism on bitcoin ['19]


- 암호화폐 거래 데이터 시각화 및 시각적 분석 연구
	[40] , [41] , [42] , [43] , [44] = 지식 대중화, 시장 견적 대시보드, 행동 분석의 세 가지 측면에서 이를 소개함


- 암호화폐 거래소의 거래 작업 유형 5가지
	- Buying & Selling:	 법정 통화로 암호화폐 매수(Buying), 암호화폐를 법정 통화로 매도(Selling)
	- Depositing & Withdrawing : 블록체인 내에서 이체 (받음 = Depositing, 보냄 = Withdrawing)
	- Crypto-to-Crypto Operation : ex/ 비트코인에서 모네로로 이체 

- 자금 세탁은 특정 기간 동안 지속되며, "다양한 금액, 여러 암호화폐, 여러 작업 유형"이 포함된 여러 거래가 포함된 3단계 프로세스임
	- 이에 대한 시간적 변화를 패턴화해야 자금세탁의 단서를 찾을 수 있음

- 자금 세탁과 관련된 거래 행위는 거래소에서 다수와 구별되는 소수 거래임
	- 따라서 자금 세탁 활동 탐지는 anomalous transaction behavior detection 문제로 분류 할 수 있음
		---> 본 논문에서는 변칙적 거래 행동을 감지하기 위해 이상 탐지와 클러스터 분석을 결합함
		1) STAGE I (Anomalous Transaction Behavior Detection)
		---> Local Outlier Factor (LOF) algorithm : 두 거래 동작 간의 거리 ---> 여기서 수식적으로 들어갔음
		2) STAGE II (Transaction Behavior Clustering)
		---> Density-based Spatial Clustering of Applications with Noise (DBSCAN) algorithm --> 소규모 클러스터를 식별할 수 있음
			+ elbow method [74] and pilot tests. 
		3) STAGE III (Anomalous Value Determination)
		---> 1단계에서 얻은 예비 이상값과 2단계에서 얻은 클러스터링 결과를 사용하여 각 트랜잭션 행동의 이상값을 최종적으로 결정
			- Behavior Anomalous Value (BAV) = 수식

- 이상 행위 값을 기반으로 Suspicious Value of an Account (ASV) 탐지
	- ASV = 


- 자금세탁 거래 전술을 분류하면
	1) loud one
		- 자금 세탁 프로세스를 빠르게 완료하기 위해 고빈도 또는 대규모 거래 수행
	2) subtle one
		- 자금 세탁 의도를 숨기기 위해, 인기 있는 암호화폐에서 정기적인 빈도와 금액으로 거래
		- 일반 계정과 유사하게 행동하기 때문에 탐지하는 것이 어려움 ★
		---> 미세한 행동 차이가 있다는데, 
			- 비교적 오래 지속되고 일정 수의 거래 행동을 수반함
			- 작은 클러스터를 형성할 것으로 예상됨
		----> 클러스터링 결과를 활용하여 subtle 자금 세탁 활동을 감지하기 위한 비정상 값을 업데이트하고자 함


- 결국, 암호화폐의 자금세탁 탐지를 위한 거래 모델링은 항상 같은 문제를 직면함!!!!##
	1) 후속 검증 작업을 위한 탐지 결과에 대한 해석 가능성!
	2) 레이블이 지정된 데이터가 너무 없음!
		---> anomaly detection으로 가던가, 해야 함  
	3) 법정 통화를 기준으로 하는 기존 연구의 feature 모델은 암호화폐와 맞지 않음!
		--> 교수님은 이거부터 파악하기를 원하시는 것 같기도 하고, 
		---> 본 논문에서는 40개의 feature로 이 시스템을 구축함


## 40개의 feature ★
- Cin 특성은 특정 기간 동안 계정의 연속 매수 또는 입금 작업의 최대 횟수입니다. 
	- 이 특성 값이 클 경우 자금 세탁 의심이 높습니다. 
	- 짧은 시간 내에 암호화폐를 연속 매수 또는 입금하는 것이 세탁자가 더러운 자금을 거래소에 넣기 위한 일반적인 거래 전술이기 때문
	--- 이런 (자금세탁)거래 전술을 원했어!!!!

- AvgAin, MaxAin 및 Cin 기능의 조합 -> 비정상적인 거래 식별 가능
	- 세 가지 기능은 모두 계정이 짧은 기간 내에 거래소에 많은 양의 법정 통화를 넣을 때 높은 값을 나타내며, 
	- 이는 자금 세탁 위험이 높다는 것을 나타냅니다.

- 자금 세탁 탐지의 오탐지 여부를 확인하기 위해 C2CFreq, C2CA, Aout 기능을 조합하여 사용할 수 있습니다. 
	- C2CFreq의 값이 매우 높으면 일반적으로 비정상입니다. 
	- 그러나 자금세탁 프로세스의 통합 단계가 없기 때문에 C2CA와 Aout의 값이 낮거나 0인 경우 자금세탁의 위험은 불확실합니다. 
	- 또한, 분석 계층 구조 프로세스[70]를 활용하여 자금 세탁 탐지에서 각 특징의 중요성을 반영하기 위해 특징에 가중치를 할당합니다. 
	- 모든 기능에 대한 설명은 이 문서의 보충 자료에 나와 있습니다. ---> ㄱㄱ


## 실제 사례가 있는 "사례 연구" -- 의심스러운 거래가 무엇이냐!
// 이 논문은 실험 쪽도 재밌네

(사례1 TruePositive; 탐지 성공 사례)
- 상위 3개 feature
	- the average amount of depositing and buying operations (AvgAin), 
	- the maximin amount of a single depositing or buying operation (MaxAin), 
	- the maximin number of consecutive depositing and buying operations (Cin)

--- 수치를 분석해보면
- 이 계정은 10일 이내에 입금 또는 매수 작업을 통해 대량의 법정 통화를 S 거래소에 입금함
	--> 이게 잠재적으로 자금 세탁 활동을 나타냄

- 후속 거래 데이터 분석에서 두 가지 핵심 사항을 검증해야 한다고
	1) 입금된 돈이 S 거래소에서 이체되었는지 여부 (*transferred out)
		---> 계정의 잔액 변화를 관찰해야 함
	2) 거래 전략은 무엇이었는가 (*trading tactic ??)
		- 분석가는 등거리 및 폴리라인(equidistance and polylines) 옵션에서 매력적인 거래 패턴 3개를 발견함
			1) 총 금액이 65,000 USDT에 이르는 BT 암호화폐에서 연속으로 5번의 매수 작업이 수행됨
			2) BT, CNYT 및 USDT 암호화폐에서 C2C와 매도 작업 간의 빈번한 전환이 발생함
			3) 2019년 12월에 발생한 BT 및 USDT 암호화폐에 대한 많은 매도 작업
		>>> 거래 전략 추론
			- BT 암호화폐로 자금을 빠르게 S 거래소에 입금하기 위해 대규모 거래를 사용함. 이는 시끄럽고 감지하기 쉬웠음
			- 이후 소액 또는 중액의 여러 C2C 거래로 자금을 계층화하고, 
			- 마지막으로 BT 및 USDT 암호화폐를 판매하여 자금을 이체함

		그럼 subtle한 세탁 거래는 실제로 감지를 할 수 있느냐가 궁금해지는데
 


(사례2: FalsePositive; 오탐)
- 높은 BAV 수치 탐지, 후속 검사 진행
- 다양한 인기 없는 암호화폐의 빈번한 C2C 거래를 포착함 = 잠재적인 자금 세탁 활동
- 하지만 이러한 C2C 거래의 금액은 적었음
- 매도 또는 출금 거래가 존재하지 않음
- 잔액 감소가 발견되지 않음
---> 자금 세탁 프로세스의 통합 단계가 없기 때문에 현재 해당 계정의 자금 세탁 위험이 낮다고 판단됨
---> 더불어, 해당 계좌는 C2C 거래가 빈번하고 관련된 암호화폐가 많기 때문에 암호화폐 투자자가 소유했을 수 있다고 유추됨



(사례3: False Negative; 미탐 the subtle one)
- 낮은 ASV 수치를 보이는 계정을 면밀히 조사해 봄 (미탐을 찾기 위해)
- 한 계정에서 상위 피처의 값 분포[그림 4(a)]가 사인파와 같은 주기성을 보임 --- 생긴게 뭔가 수상쩍긴 하네
- 두 가지 암호화폐 CNYT와 BT에서 서로 다른 거래 패턴을 발견함
	1) CNYT의 경우 잔액은 변동이 없었지만, CNTY는 주기적으로 쌍을 이루는 입출금 거래가 발견됨 (deposit & selling) (= 이체 받은 후 매도)
		-- 즉, 이체 후 빠른 매도로 잔액 변동을 포착하지 못한 것
	2) BT의 경우 2019년 10월부터 2020년 2월까지 잔액이 단계적으로 감소함
		-- 약 한 달의 간격을 두고 비교적 큰 금액의 BT 매도 거래들이 이루어짐
---> 이러한 두 가지 패턴이 자금 세탁 의도를 숨기려는 교묘한 거래 전술로 인한 것일 수 있다고 추측됨

---> 왜 미탐이 발생했는가
	-  the feature system cannot sense periodic large transactions with long intervals



## 한계 및 향후 연구

1) 시간 성능 개선: to use acceleration ideas of some DBSCAN and LOF variants [82], [83], [84] and involve parallel computation [85]
2) 핵심 파라미터인 MinPts는 알고리즘 결과에 상당한 영향을 미칩니다. 일반적으로 사용되는 엘보우 방법[74]으로 이 파라미터를 결정하지만, 실제로는 수동 미세 조정이 여전히 필요함. 탐지된 계정에 대한 개요를 제공하면 미세 조정에 도움이 됩니다 [86], [87]
3) 다른 거래소 데이터에 대한 추가 검증 필요
4) 오탐 미탐의 존재 --- 성능 측정이 어떻게 됐노

+) 레이블링과 관련하여, 계정 소유자 유형을 예측하 지도 학습 접근법 [88]

- 가장 큰 사안은 "실제" 근거가 없는 것
- 의심스러운 계정으로 분류한 계정을 '실제' 자금 세탁범으로 직접 단정할 수는 없음
	- 유죄 판결 절차는 복잡하며, 법 집행 기관의 개입이 필요함
- "고위험 계정을 찾아 법 집행 기관에 단서를 제공하는 것"이 해당 도구의 역할임

------- 법까지 넘어가면 좀 그렇지?
- 유죄 판결은, 불법적인 자금 출처에 대한 유죄 판결일거 같은데, 
- 자금세탁 행위 자체만으로 유죄가 성립하나?


- 암호화폐 거래 데이터 분석 시나리오에 대한 연구를 끝에 언급하는데, 이거 데이터 마이닝 분야인가???
	- 보안.....은 전혀 아닌 것 같지 않아? AML을 할수록 약간 정체성의 혼란이 오는데,,,,,,
	- 어떤 방향으로 연구해야 보안 연구실의 연구 같아 보일려나,,,,,







## GPT
실제 암호화폐 거래 분석가가 어떻게 의심스러운 거래를 탐지하는지 설명하는 문서나 논문이 있을까? 예를 들어 이 논문이 아주 좋은 사례야.
Is there a document or paper that explains how real cryptocurrency transaction analysts detect suspicious transactions? For example, this paper is a very good example.

- "Analysis of Bitcoin Transactions to Detect Illegal Transactions Using Convolutional Neural Networks" // feature 분석 전혀 안함
	- 랜덤 포레스트와 그래프 합성 신경망의 정보를 결합하여 비트코인 ​​거래에서 자금 세탁과 관련된 이상 징후를 식별하는 모델을 제안
		- 정확성(랜덤 포레스트)과 해석 가능성(로지스틱 회귀)을 통해 서로를 강화
		- 자금세탁에 대한 잠재적 도구로서 그래프 구조에 대해 딥러닝을 활용한 사례가 등장함
		- 암호화폐 인텔리전스 회사들이 등장하여 암호화폐 도메인을 기반으로 AML 솔루션을 제공하고 있음
		- 암호화폐를 통한 자금세탁의 가장 잘 알려진 사례 중 일부는 다음 중 하나 이상과 관련이 있습니다.
		   - 토글 스위치; 규제되지 않은 환전소; P2P 네트워크; 암호화폐 ATM; 선불 카드; 게임 사이트
		- EvolveGCN + RNN + 
		- Data: Elliptic 

- "Detecting Anomalous Cryptocurrency Transactions: An AML/CFT Application of Machine Learning-Based Forensics"
	- 방향성 그래프 네트워크로 표현된 비트코인 ​​거래를 분석하여 그래프 기반 데이터 분석 방법, 
	- 특히 그래프 합성곱 네트워크(GCN) 및 그래프 주의 네트워크(GAT)가 불법 거래를 효과적으로 분류하고 식별할 수 있음을 보여줌

- "An Abnormal Transaction Detection Mechanism on Bitcoin" // 여기는 feature 분석을 좀 하나? ㅇㅇ
	- 비트코인 ​​거래 데이터에서 사용자 특성을 마이닝하여 불법 활동을 탐지하고 이를 정상 사용자와 비정상 사용자를 구별하는 방법
		- mine the user characteristics > classify normal & abnormal users by characteristics 
			> cluster users to find suspicious users (Gaussian Mixture Model) > detect the abnormal transactions among the suspicious users
		- 비트코인 자금 세탁의 방식을 파악하고, 사용자의 정상 거래와 비정상 거래 방식을 구분하고, 
		- 비트코인 블록체인의 거래 데이터를 분석하여 사용자의 비정상 거래 여부를 탐지해야 합니다.


- "Cryptocurrency Transaction Fraud Detection Based on Imbalanced Classification with Interpretable Analysis"
	- SMOTE 과다 샘플링 및 대조 학습의 데이터 증강과 같은 기술을 활용하여 암호화폐 플랫폼에서 사기 거래를 식별하기 위한 해석 가능한 불균형 데이터 분류 방법



- shadow 거래??
- 자금 서비스 기업(MSB)


