---
title: "[Note] 25-01-09 feature_engineering.txt"
categories: [Notepad, Daily]
tags: 
date: 2025-01-09 21:49:00 +0900
comments: false
---
---


연구를 설계할 때는 구체적이고 영향력 있는 연구 문제를 다루는 것이 필수적
현재 방법의 한계에 대한 요약과 함께 타겟팅할 수 있는 잠재적인 문제


* 크로스 체인 분석 부족: 여러 체인 간의 거래 추적을 통합하는 프레임워크★
* 설명 가능성의 부족: 설명 가능한 머신 러닝 모델★
* 적응력 부족: 새로운 자금 세탁 전략에 대응하기 위한, 적응적인 모델★ 거래 전략 추론!

>> Adaptable and Explainable Money-Laundering Detection in Cross-chain Cryptocurrency


* 믹서 활동 감지
* 거래 구조(Layering) 감지


* 온체인 및 오프체인 데이터 결합 (IP 주소, 사용자 메타데이터, 거래 로그)
* Feature Engineering on Cross-chain Cryptocurrency Money-Laundering Transaction
* 하위 그래프 구조 [4] 
* 복잡한 거래 패턴을 데이터 구조적 접근방식


* 데이터가 문제여

* FATF 권장 사항을 포함한 자금세탁방지(AML) 프레임워크에 대한 지식

____________________________________________________________________________

[3] An Abnormal Transaction Detection Mechanism on Bitcoin [International Conference on Networking and Network Applications ‘19] ★★★★★★★★★
// user characteristic mining --> 비정상 사용자 탐지 (자금세탁자, 도둑)
// 

- 비트코인 ​​거래 데이터에서 "사용자 특성을 마이닝"하여 "불법 활동을 탐지"하고 이를 정상 사용자와 비정상 사용자를 구별하는 방법
	> mine the user characteristics 
	> classify normal & abnormal users by characteristics 
	> cluster users to find suspicious users (Gaussian Mixture Model) 
	> detect the abnormal transactions among the suspicious users




## 데이터
- 블록 데이터 추출을 제공하는 블록체인 검색 엔진인 Blockchair [7] -> 파싱된 블록 데이터 덤프 파일 사용


## feature engineering 10가지

- Number of Addresses(NOA), represents the number of addresses owned by the user.
	사용자가 소유한 주소의 수
- Number of Transactions(NOT), represents the number of user transactions.
	사용자 거래 수
- Average Number of Input Addresses(ANOIA), represents the average number of user addresses as input addresses in a transaction.
	거래에서 입력 주소로 사용되는 평균 사용자 주소 수
- Average Number of Output Addresses(ANOOA), represents the average number of user addresses used as output addresses in a transaction.
	거래에서 출력 주소로 사용되는 평균 사용자 주소 수
- User In Degree(UID), represents the number of senders when the user is the recipient.
	사용자가 수신자일 때 보낸 사람의 수
- User Out Degree(UOD), represents the number of recipients when the user is the sender.
	사용자가 발신자일 경우 수신자의 수
- Average Receiving Amount(ARA), represents the average amount of user input addresses receive in a transaction.
	거래에서 사용자 입력 주소가 수신하는 평균 금액
- Average Sent Amount(ASA), represents the average amount of user output addresses send in a transaction.
	거래에서 사용자 출력 주소가 전송하는 평균 금액
- User Active Time Range(Uatr), represents the time interval between the user's first transaction and the last transaction.
	사용자의 첫 번째 거래와 마지막 거래 사이의 시간 간격
- Address Average Active Time Range (AAATR), represents the average time between the first transaction of a user's address and the last transaction.
	사용자 주소의 첫 번째 거래와 마지막 거래 사이의 평균 시간



## 사용자를 6가지로 구분함

- 정상: 소비자(CR), 장기 투자자(LI), 단기 투자자(SI), 얼리어답터(EA) 
- 비정상: 자금 세탁자(ML), 도둑(TF) 



## GMM
- GMM은 클러스터링에 일반적으로 사용되는 비지도 머신 러닝 알고리즘

GMM)을 사용하여 사용자의 10가지 특징 데이터를 기반으로 사용자를 클러스터링
클러스터 특성을 분석하여 정상 클러스터와 의심 클러스터를 구분
의심 클러스터의 사용자 거래를 알려진 비트코인 ​​도난 사례[9]와 비교하여 결과를 검증
	## 통계에 따르면 2022년 해킹으로 인해 약 20억 달러의 암호화폐가 도난당했습니다 .
	## 범죄자들은 ​​2021년에만 86억 달러의 암호화폐를 세탁했습니다 .  https://www.cato.org/blog/overstating-crypto-crime-wont-lead-sound-policy


// 악성 거래 탐지 관련 선행연구
- Pham과 Lee [10] 의 연구는 K-평균 클러스터링, Mahalanobis 거리, 비지도 지원 벡터 머신(SVM)을 적용하여 익명성 관점을 조사
- Monamo 등의 연구 [11]는 유사한 접근 방식을 취했으며 트리밍된 K-평균 클러스터링을 사용하여 거의 동일한 결과
- Hirshman 등의 연구 [12]는 K-평균을 사용하여 거래 데이터 세트를 클러스터링하고 자금 세탁 시도 탐지
	+믹싱 서비스의 출력을 입력으로 추적하려고 시도
	- RolX를 사용하여 비정상적인 동작을 식별할 수 있었지만 믹싱 서비스 사례를 식별하지 못함

-- 선행 연구들에 대한 저자의 평가
1) 원본 트랜잭션 데이터에서 휴리스틱 사용자 주소 클러스터링이 수행되지 않으며, 
	사용자 주소 집합으로 완전히 분할되지 않아 사용자 클러스터 범위의 정확도가 낮아졌음
2) 사용자에 대한 feature 마이닝을 상세히 수행하지 않았으며, 정상 사용자와 비정상 사용자 간의 구분 조건을 명확하게 제공하지 않음
3) 사용된 알고리즘들이 밀도를 추정하는 능력이 부족함
4) 선행 연구들의 방법은 비정상 거래를 감지하는 데 그다지 효과적이지 않음





## 흥미로운 키워드

A. 의심스러운 사용자 네트워크 구조
	- 의심스러운 사용자들 간의 내부 연결 [13]
		"Microthings: A generic iot architecture for flexible data aggregation and scalable service cooperation", IEEE Communications Magazine, pp. 86-93, 2017.
	- 의심스러운 사용자 거래 네트워크 그래프 [14] 구성하기
		"Understanding ethereum via graph analysis", IEEE INFOCOM 2018-IEEE Conference on Computer Communications, pp. 1484-1492, 2018.
	- 불법적 사건의 발생과 상관관계가 있는지 탐지하고, 그렇다면 그들 사이에 어떤 종류의 연결이 있는지 탐색해야 한다 [15] .
		"Quantitative analysis of the full bitcoin transaction graph", International Conference on Financial Cryptography and Data Security, pp. 6-24, 2016. ☆☆☆
B. 비정상적 거래 모드
	- 비정상 사용자는 비트코인 ​​집계, 분할 및 기타 후속 작업을 수행하는 경향이 있습니다 [16]
		"An inquiry into money laundering tools in the bitcoin ecosystem", eCrime Researchers Summitt(eCRS), pp. 1-14, 2013.
	- 후속 비정상 거래 패턴을 추가로 탐색 및 추적하고, 비정상 거래 패턴을 모델링하여 불법 사건의 후속 자본 흐름을 예측




## Blockchair
- 저희는 사용자 개인 정보 보호를 중시합니다. 저희는 웹사이트에 타사 추적기를 두지 않으며 Tor에 대한 .onion 서비스를 제공합니다
	--- 띠용, 사용자 개인정보보호를 위해 Tor 서비스를 제공해준다?!



--------------------------------------------------------------------------------


암호 자산 서비스 제공자(CASP)

the Features about Supervising cryptoassets for anti-money laundering



- 불법 암호화폐 거래소 = 고객 및 거래 식별 X
- 암호화폐의 범죄적 사용 퇴치를 위해
- 거래 모니터링 도구 개선 필요 ------------> 어떤 점을 개선해야 되냐 이거지


--------------------------------------------------------------------------------

##뉴스
한 금융연구소 관계자는 
"중국의 전면 금지와 미국의 상대적 개방 사이에서 한국은 균형 잡힌 규제 방향을 설정해야 한다"며 "투자자 보호와 산업 발전을 동시에 고려한 제도 마련이 필요하다"고 강조했다.
	- 제도는 금융/정치 문제고
"한국 거래소들이 규제 준수와 기술력을 바탕으로 경쟁력을 강화할 필요가 있다"고 제언했다.
	- 거래소에 적용할 수 있는 기술력/경쟁력?
더불어 자금세탁 방지 등 리스크 관리체계 구축도 중요한 과제로 꼽힌다. ★★★이게 좀 거시적인 연구 목표
	- ML에 대한 리스크 관리체계 구축 ☆


"암호화폐가 외환 규제 회피 수단으로 악용될 수 있다"며 "체계적인 모니터링 시스템 구축이 필요하다"
	- 암호화폐를 구매한 뒤 해외 법정화폐로 교환하는 관행
	- 암호화폐 악용 사례에 대한 체계적인 모니터링 시스템
		- 이를 위한 기술력!을 연구해야! (for 암호화폐 거래소 like 빗썸, 업비트)
			- 디지터자산거래소협회(DAXA)
// 이런게 교수님이 원하시는 문제 시나리오인가?
// 아니지, 이건 외환규제 회피 수단으로 암호화폐를 이용한 경우이고
// 나는 자금세탁 수단으로 암호화폐를 이용한 경우로 픽스하고 들어간거니까
// 가져가야 할 문제 시나리오는 암호화폐로 ML를 하는 방법에 대한 시나리오인게 맞지
// [2] Visual Analysis 논문에 있는 시나리오가 맞음!
	- 암호화폐 거래소에서 거래 감독원이 자금세탁 거래를 식별하는 방법! ★★★이게 연구 도메인 (=문제 상황 인식)
		-- 현재 식별 기술 및 현기술의 한계점 조사 = 연구 문제 정의

----------------정리해보면

- 중국의 전면 금지와 미국의 상대적 개방 사이에서 한국은 암호화폐에 대한 균형 잡힌 규제 방향을 설정해야 한다.
- 자금세탁 방지 등 리스크 관리체계 구축에 대한 한국 거래소들의 규제 준수 및 기술력 강화가 필요하다.
- 그래서 내 연구의 도메인은 "암호화폐 거래소에서 거래 감독원이 자금세탁 거래를 식별하는 방법"

따라서 TODO
1) 현재 식별 기술 및 현기술의 한계점 조사	// AML 분야 연구 동향 깔끔하게 정리하고 싶다...... Goal, Problem, Method, Dataset, Github으로 나눠서 엑셀 정리 ㄱ
	[2] Visual Analysis (2024) -----> 이 사람 연구에서 내가 발전시키고자 하는 문제가 뭐야★★★ 거래 전술에 초점을 맞춰서? 능동학습 되나?
	[3] Abnormal Transaction Detection Mechanism (2019)
	[4] Subgraph Contrastive Learning (2024)

2) 데이터셋 조사
	- Elliptic [공개]
	- Elliptic++ [↑데이터 확장 논문]
	- Blockchair [덤프]
	- BlockSec [사기업, 유료?] [근데 흥미로운 데이터 구조]
	- more...?

3) Method 고민 	// ---> 그니까, 이 method가 해결할려는 구체적인 연구적 문제가 무엇인지!! 그것이 지금 필요한 게 아닐지!!
	- Feature Engineering [2,3]
	- GNN (中 하위 그래프 수준 탐지[4])
	- 샘플링 / 라벨링 / 학습 관련 기술




+) 여러 중개자가 관여하는 여러 계층의 거래
익명의 블록체인 흔적?
 "혼합 서비스" 식별, 체인 전반의 지갑 추적, 거래 계층 감지와 같은 과제에는 컴퓨터 엔지니어링 전문 지식과 일치하는 그래프 분석 및 AI와 같은 최첨단 도구가 필요함

Algimantas Venčkauskas

----------------


"Are Suspicious Activity Reporting Requirements for Cryptocurrency Exchanges Effective?" ['21]
거래소의 보고 행동을 평가하는 이론적 모델


"Demystifying Fraudulent Transactions and Illicit Nodes in the Bitcoin Network for Financial Forensics" [ACM SIGKDD '23]
시간적 상호작용을 포함하는 확장된 데이터 세트 Elliptic++를 제시


"Characterizing and Detecting Money Laundering Activities on the Bitcoin Network" [arXiv '19] (후이닝씨 논문이었)
그래프 피처에 기반한 분류기를 제안하여 세탁 거래와 일반 거래를 구별
	- 세탁 거래와 일반 거래의 주요 차이점은 출력 값과 이웃 정보에 있다는 것을 발견했습니다.
	- 직접 이웃, 큐레이트된 피처, 딥워크 임베딩, node2vec 임베딩☆의 네 가지 유형의 그래프 피처를 기반으로 하는 분류기 세트를 제안


"Anti-Money Laundering in Bitcoin: Experimenting with Graph Convolutional Networks for Financial Forensics" ['19]
불법 거래를 탐지하기 위해 GCN을 포함한 다양한 머신 러닝 모델을 평가


Improving the accuracy of money laundering detection in the Bitcoin network ['20] (후이닝씨)
샘플이 거의 없는 소수 클래스로 인해 불균형한 데이터 세트를 분석할 때 분류기의 성능을 향상시키려는 여러 메커니즘을 통해 비트코인 ​​네트워크의 자금 세탁 활동을 조사
	- Elliptic

[4] Bitcoin Money Laundering Detection via Subgraph Contrastive Learning [Entropy '24] ★★★★★★★★★
자금 세탁 그룹 탐지를 수행하기 위해 Bit-CHetG라는 이질 그래프를 위한 새로운 하위 그래프 기반 대조 학습 알고리즘을 제안
	- 이 논문에서는 비트코인 ​​자금 세탁의 조직적이고 이질적이며 노이즈가 많은 특성이 제기하는 과제를 해결합니다.
		- 지갑 주소와 거래 기록의 동질 하위 그래프를 구성하여 이질성을 포착하는 능력을 향상
		- 그래프 신경망을 사용하여 거래 하위 그래프의 위상 임베딩 표현과 거래 노드의 관련 주소 표현을 별도로 추출
		- 동일한 클래스의 트랜잭션 서브그래프를 모으는 동시에 다른 클래스의 서브그래프를 밀어내는 노이즈의 효과를 줄이기 위해 감독 대조 학습 도입

	- 암호화폐 자금세탁 방지(AML)는 규칙 기반 방법, 비지도 이상 탐지 방법, 지도 학습 방법의 세 가지 범주로 나뉩니다.
		- 클러스터링 + 어노말리 = 2 = 연구에 따르면 이러한 알고리즘의 효과성은 지도 학습 AML 알고리즘과 비교할 수 없습니다
		- 결국 지도학습이 답이다? 레이블링 데이터를 만들어야 한다?
	- 3 중에서 그래프 관련 알고리즘은 node2vec 기반 분류기[ 15 ], 그래프 합성 신경망(GCN)[ 16 ] 및 이들의 변형[ 17 , 18 , 19 ]을 포함하여 매우 우수한 성능을 보입니다
		- 가장 큰 지도 비트코인 ​​데이터 세트 = Elliptic -> 노드 분류 작업
	---- 그럼 그래프 구조를 이용해 암호화폐 자금세탁을 감지하는 것에는 어떤 문제가 있느냐★ ----근데 이건 이제 GNN으로 지도학습 방법 써서 탐지하겠다 했을 때 
		- 불법 행위의 조직적이고 이질적이며 잡음이 많은 특성! ★★★★ 자금세탁 활동의 특성인거지
			1) 조직화: 자금 세탁은 일반적으로 조직적인 행동이다. ☆☆☆☆---> 이거 [3]에서 향후 연구과제로 언급한 내용이네?
				- 따라서 현재의 노드 수준 탐지 알고리즘은 맞지 않다. 그룹 탐지로 가야 한다.
				- 공개된 대규모 암호화폐 자금 세탁 사례(예: 1MDB[ 21 ] 및 Danske Bank 스캔들[ 22 ])
				- 하위 그래프 수준 탐지 방법은 노드 간의 토폴로지를 고려 & 유사한 거래 패턴을 가진 하위 그래프를 식별할 수 있음
			2) Heterogeneous: 이질성은 암호화폐 거래 네트워크의 고유한 특성[ 24 ].
				- 이질성은 데이터 마이닝의 복잡성을 증가시켜 더 복잡한 위험 식별 프로세스로 이어짐
				- 현재 연구는 거래 기록 그래프[ 20 ] 또는 지갑 주소 그래프[ 23 ]에 초점을 맞추고 있는데
					- 흠, 데이터 구조에 대한 분석
			3) 잡음: 이라 함은, 합법적인 잘못된 거래와 자금 세탁자가 자신의 활동을 은폐하기 위해 시작한 의도적인 방해 거래[ 25 ]

	- 이질적 정보를 기반으로 하는 하위 그래프 수준 그래프 대조 학습 알고리즘, 즉 대조적 이질적 그래프 신경망(Bit-CHetG)을 설계합니다.
	- Bit-CHetG는 거래 하위 그래프  임베딩(TSE), 주소 피처 집계(AFA), 피처 융합(FF), 대조 학습(CL)의 네 가지 주요 구성 요소로 구성됨
	- 5.2절 에서 설명한 것처럼 발견된 패턴은 자금세탁 집단과 비자금세탁 집단을 효과적으로 구별할 수 있다

	- 통화 흐름에 대한 방향성 정보 고려
	- 거래 네트워크 토폴로지의 역동성???? 시간 정보를 통합한 동적 그래프 신경망? 시간정보 안 들어갔어?









- 거래 분석해서, Crypto "Wash Trading" 탐지까지 되나?
	-  manipulative tactics(시장조작 전술): algorithmic wash trading, pump and dump schemes, ★★★★이것도 좀 알아보고싶으넫

DeFiRanger: Detecting DeFi Price Manipulation Attacks [IEEE Transactions on Dependable and Secure Computing '23]

+) 암호화폐 거래소에서 "시장조작 활동(전술)" 탐지? ㄴㄴㄴ시장 무결성으로 가버림 AML과는 무관		// AML 준수는 정부, 거래소 및 금융 기관의 최우선 과제
	----- 이쪽 관련해서 feature engineering으로 들어가봐?! 탐지 타겟이 시장공격 쪽으로 바뀐거지.
	- algorithmic wash trading (본인매도매수): 거래소 내 거래로 이루어짐 = 굿 --> 의심스러운 높은 거래량과 반복적인 주문 패턴
	- pump and dump schemes (대대적 홍보로 가격 키우기): 외부정보가 키워드라서 패스
	- spoofing (가짜매도): 가격 낮추는 매도 주문 넣고, 그래프가 반응하면 주문 취소? 코인에서도 가능함? --> 대량 주문이 실행되기 전에 갑자기 취소
	----- 참고할 feature: 주문 크기, 빈도, 타이밍, 거래 간 상관 관계, 주문유형, 지갑주소, & 거래흐름
	----- 평가방법: 정확성을 위해 규제 기관의 조사 결과나 고발자 보고서와 비교
	----- 투자자, 규제 기관, 거래소에 미치는 잠재적 영향s





Detecting and Quantifying Wash Trading on Decentralized Cryptocurrency Exchanges [WWW '21]	// wash trading으로 안가도, 구조 분석한거 참고하면 좋을듯
- 논문 그림이 멋지다 -- 거래 및 계정 구조를 분석한건가?
- 워시 트레이딩의 법적 정의를 충족하는 계정 및 거래 구조의 하한을 식별




https://link.springer.com/epdf/10.1007/s00521-024-10510-w?sharing_token=qzYDN0wkgyM62q3IGV69p_e4RwlQNchNByi7wbcMAY5Npbxnvc6OIMld1H9d9y3B_42V4PxDAGb3SB8z_ixY9CZlPZ_HH1kz4eK7paZ5iQ-zKM5tyF1WduCEaVLNRwpYt2LHSHIrlsIgcjjmrIl_GWTJmXBIOAUl1yc7LvstEtI%3D



