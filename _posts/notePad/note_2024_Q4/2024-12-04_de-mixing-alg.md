---
title: "[Note] 2024-12-04 de-mixing-alg.txt"
categories: [Notepad, Daily]
tags: 
date: 2024-12-04 21:49:00 +0900
comments: false
---
---

Bitcoin Mixing Detection Solution
________________________________________

## 익명화와 AML에 대한 내 생각

--- 익명화에 대한 견해? 입장차이? 
--- blockchain은 익명성을 표방하는가? 
--- 블록체인 기술만으로는 그래프 알고리즘으로 익명성이 쉽게 해제됨
--- BC의 익명성 강화를 위해 mixer들이 연구되고 있는데, 해당 연구의 취지가 무엇인지
	 -> 이건 이념적인 관점? 뭐라해야하지, DeFi가 등장한 이유 같은 느낌. 탈중앙? +개인 프라이버시 존중?
	- There plenty of legitimate reasons for why one would want to use a mixing service to preserve anonymity of one’s spending. (논문 발췌)
--- 반대로 익명성을 없애려는 연구는 어떤 정당성이 있는지 -> 좀더 윤리적인 관점에서 시작하는 듯. 뭐가 더 우선하는지?
--- 이걸 정책으로 안 막고 기술로 막는게, 맞나? 정책으로 해결이 안되는 부분이라서 기술로 가는건가? 자유권 뭐 그런거? --> 논문에서 언급된다


정리해보면, 

1. 블록체인과 익명성
- 블록체인은 거래 기록의 투명성을 표방하지만, Pseudonym(가명)을 통해 익명성을 제공한다.
- 그러나 그래프 알고리즘 및 데이터 분석 기술을 통해 익명성을 해제할 수 있다.
- 이를 보완하기 위해 Mixer와 같은 기술이 등장했으며, 이는 개인의 프라이버시와 자율성을 강화하려는 시도다.
- 하지만 이러한 기술이 항상 정당하게 사용되는 것은 아니며, 자금세탁과 같은 부정적 활용 사례도 존재한다.

2. 익명성이 왜 필요한가
- 익명성을 강화하려는 입장	
	- 개인의 프라이버시를 보장하고 금융 활동에서의 자유를 보호하려는 목적이 있다.
	- 이는 DeFi의 철학과 맞닿아 있으며, 중앙화된 권력의 간섭을 배제하려는 이념적 배경을 가진다.
- 익명성을 해제하려는 입장
	- 자금세탁, 테러 자금 조달, 불법 거래와 같은 문제를 해결하기 위해 필요하다.
	- 이는 윤리적이고 사회적인 책임을 기반으로 하며, 공공안전을 우선시한다.

3. 기술적 접근과 정책적 해결 관점
- 정책적 접근은 제도적 통제와 윤리적 기준을 제시할 수 있지만, 글로벌하게 동작하는 암호화폐 생태계에서는 효과가 제한적일 수 있다. 아마도.
- 왜 규제가 제대로 작동하기 어려운지에 대한 내용은 논문에 있더라

4. Mixing Service의 정당한 사용과 오용에 대해
- 믹싱 서비스는 개인의 프라이버시와 익명성을 강화하는 도구로 설계되어지만, 부정적 목적으로 악용될 가능성이 높다.
- 이러한 양면성을 고려할 때, 믹싱 서비스의 정당성과 오용 가능성 사이의 균형을 찾는 연구가 중요하다.
- 법적/윤리적 판단뿐 아니라, 기술적 솔루션을 통해 오용을 최소화!
	- 그 접근법이..... 영지식 증명? 


==============================================================
##### 사토시 빨래방['23]에 등장하는 믹서 논문

-------------------------------------------------------------------------------------------------
믹서 특징: 1. 수수료 유무(->무작위) 2. 시간 지연 (->무작위) 3. 중앙 집중 유무 (-> ?반반)
1. Hong et al. (2018) : 고정 수수료의 De-mixing alg.
2. Ziegeldorf et al. (2018) : 시간지연 연구
3. (2013) https://bitcointalk.org/index.php?topic=279249 : 코인조인 등장
	중앙집중식 믹서는 관리기관이 받아서 처리하는 로그가 있음
	탈중앙 믹서는 프로토콜에 의해 자동 처리
-------------------------------------------------------------------------------------------------


## A Practical De-mixing Algorithm for Bitcoin Mixing Services [BCC '18] (Hong et al.)
	# De-mixing Algorithm 
- 가장 널리 사용되는 비트코인 ​​믹싱 서비스 중 하나인 Helix에 대한 믹싱 해제 알고리즘을 제안
- 고정 수수료라서 가능했던 것 (= 입출력 비트코인 잔액으로 파악이 가능함)



## Secure and anonymous decentralized bitcoin mixing (Ziegeldorf et al.) (2018)
- 시간지연 연구, 당연히 무작위 지연하는 게 더 분석하기 어렵겟지요



## Popular decentralized Bitcoin mixers such as Wasabi Wallet and Samourai Whirlpool incorporate CoinJoin (Maxwell, 2013) into their mixing protocols.
- CoinJoin 개념이 처음 등장한 듯 : https://bitcointalk.org/index.php?topic=279249 ★
	- 코인조인이란: 
	- 왜 이 기술이 필요하다고 봤는지: 


## Everything You Ever Wanted to Know About Bitcoin Mixers (But Were Afraid to Ask) ['21] ★★
	# 실제 사용되고 있는 Bitcoin Mixers에 대해 
- 최근 몇 년 동안 다양한 중앙 집중화 및 분산형 비트코인 ​​믹싱 방법이 학술 문헌에 제안되었습니다(예: CoinJoin, CoinShuffle).
- 오늘날의 믹싱 서비스가 학계에서 제안한 솔루션을 채택했는지 확인하기 위해 공개 비트코인 ​​믹서 생태계를 살펴봅니다.



## Discovering bitcoin mixing using anomaly detection (Prado-Romero et al.) (2018)
- higher inter-community linkage // 커뮤니티 내 연결성이 무슨 말인지?
- 믹싱 계정을 식별할 수 있다는 것은?


## Detecting mixing services via mining bitcoin transaction network with hybrid motifs (Wu et al.) (2022) ★★
- 믹싱 데이터에 라벨을 붙여서 패턴을 찾아냄	// 어떻게 라벨을 붙였을까
	1. 믹서는 목적지 주소로 지급하기 전에 입력 거래를 먼저 받는 경향이 있다.	// 무슨의미
	2. 믹서는 동일한 주소를 거의 재사용하지 않는다.
	3. 믹서는 오염된 비트코인을 다른 사람에게 지급하는 것을 선호한다.	// 무슨 의미
	4. 라벨이 붙은 믹싱 주소를 보니, 일반적으로, 입력 거래 금액이 출력 거래 금액과 같아서 잔액이 0을 유지한다.  // 왜?
	5. 라벨이 없는 주소는 평균적으로 positive balance를 유지한다  // 평균적이라 함은, 마이너스 잔액이 있다? 애초에 마이너스 잔액이 가능한가??
- 믹싱 서비스에서 만든, 믹싱에만 사용되는 주소가 있나봐
- 이 분석 결과가 어떤 의미를 가질 수 있을까 싶구만, 후속 연구가 있을지?


## Mixing detection on bitcoin transactions using statistical patterns (Shojaeenasab et al.) (2022)
- sweeper transaction? -> 다수 입력 & 1~2개 출력
- Pay to Script Hash (P2SH) address : 비트코인 거래 주소 -> 3으로 시작, 다수 소유자? 
	- 레거시라는데 요즘 쓰는건 P2WSH 
- P2SH 이걸로 sweeper trx 탐지 가능




## Bitcoin mixing detection using deep autoencoder (Nan and Tao) (2018) ★★★★
- identify mixing services through anomaly detection 
- feature extraction using deep auto-encoder for community detection
- O(n2) -> not practical
- 그래프 임베딩 알고리즘 중 딥 오토 인코더를 이용한 노드 인베딩 방법
- 그래프 클러스터링을 사용하여 믹싱 서비스 주소를 직접 분류
--- [9] 는 처음으로 문제를 식별하고 해결책을 제시했는데, 이는 먼저 이상 탐지를 사용하여 전체 거래 그래프를 클러스터링한 다음 이상치 순위라는 노드 기능을 사용하여 혼합 서비스에 속하는 노드의 가능성을 평가하는 것이었습니다. 그러나 그들의 작업은 노드 기능을 휴리스틱하게 선택했기 때문에 그래프의 구조와 커뮤니티 정보를 충분히 활용하지 못했습니다.


## LSTM-TC: Bitcoin coin mixing detection method with a high recall (Sun et al.) (2022)
- 규칙 기반 탐지 한계
- 솔루션 속도 > 블록 생성 속도
- Long Short-Term Memory tree classifier
- 머야 detection of mixing services에 대한거였음
	- 이건 AML 조사에 대한 방향성만 제시(??)하는거라 블랙박스여도 ok임



## SoK: assumptions underlying cryptocurrency deanonymizations (Deuber et al.) (2022)
- Deanonymization attacks (비익명화 공격 != 재식별 공격)



## Anonymous bitcoin transactions (Maduakor) (2017)
- 중앙화 믹서를 통과한 거래 추적 크롤러 구현



## Everything you ever wanted to know about bitcoin mixers (but were afraid to ask) (Pakki et al.) (2021)
- 요즘 상업 믹서들은 dynamic mixing fees를 쓴다




# 규제기관에서 mixer 서비스를 중단시켜버리는 방법
- 중단시켜버릴려면, 실제로 엄청난 자금세탁을 유발하고 있다는 증거가 있어야 한다고.
- 익명처리 자체는 불법이 아니라서, 익명성 속에서 불법활동에 속하는 자금세탁이 이루어지고 있다는 걸 증명해야지 제재가 되는 것으로 보임
- 그럼, 자금세탁....즉 연결성이 끊어져버렸다 흠. 이게 익명성인데,,, 자금 출처가 불법 활동에 기반했다는 사실을 증명해야 하자나? 흠 동형암호가 생각나네

- 재식별이랑 비익명화랑 다르다고 한 논문을 한번 봐야겠다

- 아 출처는 시작 지점만 식별하면 되는구나



암호화 관련 내용인가? (Young et al., 2021)

## Adoption and actual privacy of decentralized CoinJoin implementations in bitcoin (Stütz et al., 2021)
- 코인조인 거래를 식별하는 것은 가능하지만, 코인조인 거래를 실행한 믹싱 서비스가 뭔지 파악하는 것은 어렵다 
----- 어떤 믹싱 서비스를 중지시킬지 파악해야 하니까??


## Detecting roles of money laundering in bitcoin mixing transactions: a goal modeling and mining framework (Liu et al., 2021) ★★
- 믹싱 서비스에서의 자금세탁 규모 추정 프레임워크 구현 
- 믹싱 프로세스 내의 주소들을, 목표 범주에 따라 에이전트로? 분류할 수 있따.
	= 에이전트 역할 분석을, 비익명화 과정 없이 가능하다


-------------------------

결론
★ 요즘 믹싱 서비스는 off-chain 기술을 써서 추적이 더 어렵다! --> 오프체인 기술 파훼법 필요
★ 개인 프라이버시를 존중하면서 자금세탁 문제를 해결할 혁신적인 방법론이 필요하다 --> 영지식 증명 ?


------------------------- 논문 리스트 정리
2017
- Anonymous Bitcoin Transactions (Maduakor)
	- 중앙화 믹서를 통과한 거래 추적 크롤러 구현.

2018
- A Practical De-mixing Algorithm for Bitcoin Mixing Services [BCC '18] (Hong et al.)
	- Helix 믹싱 서비스의 믹싱 해제 알고리즘 제안.
- Secure and Anonymous Decentralized Bitcoin Mixing (Ziegeldorf et al.)
	- 시간지연 연구 및 무작위 지연의 효과 분석.
- Discovering Bitcoin Mixing Using Anomaly Detection (Prado-Romero et al.) ★
	- 비트코인 믹싱 계정을 식별하기 위한 이상 탐지 연구.
- Bitcoin Mixing Detection Using Deep Autoencoder (Nan and Tao) ★★
	- 딥 오토인코더를 사용한 커뮤니티 탐지 및 비트코인 믹싱 서비스 식별.

2021
- Everything You Ever Wanted to Know About Bitcoin Mixers (But Were Afraid to Ask) (Pakki et al.) ★★
	- Bitcoin mixers의 동작 방식 및 동적 믹싱 수수료 개념 탐구.
- Adoption and Actual Privacy of Decentralized CoinJoin Implementations in Bitcoin (Stütz et al.)
	- CoinJoin 거래 식별 가능성과 믹싱 서비스 파악의 어려움 연구.
- Detecting Roles of Money Laundering in Bitcoin Mixing Transactions: A Goal Modeling and Mining Framework (Liu et al.) ★★
	- 자금세탁 역할을 추정하는 목표 기반 분석 프레임워크 구현.

2022
- Detecting Mixing Services via Mining Bitcoin Transaction Network with Hybrid Motifs (Wu et al.) ★★
	- 하이브리드 모티프 분석을 통한 믹싱 서비스 탐지 연구.
- Mixing Detection on Bitcoin Transactions Using Statistical Patterns (Shojaeenasab et al.) ★
	- 통계적 패턴 분석을 활용한 비트코인 믹싱 탐지.
- LSTM-TC: Bitcoin Coin Mixing Detection Method with a High Recall (Sun et al.) ★
	- LSTM 기반 모델을 통한 고정확도의 믹싱 서비스 탐지.
- SoK: Assumptions Underlying Cryptocurrency Deanonymizations (Deuber et al.) ★
	- 암호화폐 비익명화 공격에 관한 가정 검토.

------------------------- 사토시 빨래방 정리

## 자금세탁거래 식별 방법 ★★★★★★★★★★★★
- 거래 그래프 분석
	- 이상 탐지를 위한 머신 러닝
	- 그래프 클러스터링
	- 패턴 인식
	- sweeper 거래 ? 휴리스틱ㅇ니가
- 머신 러닝 접근 방식
	- 지도 학습과 반지도 학습
	- 경험적 및 경험적 접근 방식
- 익명 해제 기술


## 한계점
- 진화하는 믹싱 서비스: 시간이 지남에 따라 휴리스틱은 신뢰할 수 없게됨
	---> 적응력 있는 AI 모델을 만들어야?
- 계산 복잡도: 딥 오토 인코더와 같은 높은 복잡성 방법은 대규모 그래프에는 실용적이지 않음 
	---> 블록생성 속도와 비교해야 한다는 내용이 있었음
- 데이터 라벨링 제한 사항: 머신 러닝 모델을 학습하는 데 필요한 레이블이 지정된 데이터 부족 
	---> 능동학습 같은 학습 방법 탐색?
- 블랙박스 모델: LSTM과 같은 머신 러닝 모델은 설명 가능성이 부족하여 AML 규제의 근거로 들기 부족함


------------------------- 익명화와 AML에 대한 내 생각 정리

1. 블록체인과 익명성
- 블록체인은 거래 기록의 투명성을 표방하지만, Pseudonym(가명)을 통해 익명성을 제공한다.
- 그러나 그래프 알고리즘 및 데이터 분석 기술을 통해 익명성을 해제할 수 있다.
- 이를 보완하기 위해 Mixer와 같은 기술이 등장했으며, 이는 개인의 프라이버시와 자율성을 강화하려는 시도다.
- 하지만 이러한 기술이 항상 정당하게 사용되는 것은 아니며, 자금세탁과 같은 부정적 활용 사례도 존재한다.

2. 익명성이 왜 필요한가
- 익명성을 강화하려는 입장	
	- 개인의 프라이버시를 보장하고 금융 활동에서의 자유를 보호하려는 목적이 있다.
	- 이는 DeFi의 철학과 맞닿아 있으며, 중앙화된 권력의 간섭을 배제하려는 이념적 배경을 가진다.
- 익명성을 해제하려는 입장
	- 자금세탁, 테러 자금 조달, 불법 거래와 같은 문제를 해결하기 위해 필요하다.
	- 이는 윤리적이고 사회적인 책임을 기반으로 하며, 공공안전을 우선시한다.

3. 기술적 접근과 정책적 해결 관점
- 정책적 접근은 제도적 통제와 윤리적 기준을 제시할 수 있지만, 글로벌하게 동작하는 암호화폐 생태계에서는 효과가 제한적일 수 있다. 아마도.
- 왜 규제가 제대로 작동하기 어려운지에 대한 내용은 논문에 있더라

4. Mixing Service의 정당한 사용과 오용에 대해
- 믹싱 서비스는 개인의 프라이버시와 익명성을 강화하는 도구로 설계되어지만, 부정적 목적으로 악용될 가능성이 높다.
- 이러한 양면성을 고려할 때, 믹싱 서비스의 정당성과 오용 가능성 사이의 균형을 찾는 연구가 중요하다.
- 법적/윤리적 판단뿐 아니라, 기술적 솔루션을 통해 오용을 최소화!
	- 그 접근법이..... 영지식 증명? (사토시 빨래방의 결론)
	- AI?

5. 해야하는거
- off-chain 기술, 랜덤 수수료, 랜덤 시간 지연을 사용하는 믹싱 서비스를 대상으로 한 De-mixing alg 또는 AI 모델
- mixed transaction에 정상 거래도 있고, 불법 자금세탁 거래도 있는데, 불법 자금세탁 거래를 식별하는 방법이 무엇인지
- 혹시 영지식증명이 사용될 수 있을지

----------------------------------------


AI 알고리즘은 거래 데이터에서 비정상적인 패턴을 식별하고, 새로운 자금세탁 방법에 적응하며, 거짓 양성 판정을 줄일 수 있음
Chainalysis나 Elliptic과 같은 도구는 블록체인 거래를 분석하여 불법 자금을 추적하고 위험한 지갑을 식별함


## 아무튼 분석대상은 익명화 씨게 해둔 Monero, Zcash
- 대부분의 암호화폐(예: 비트코인)는 익명 주소를 사용합니다. 즉, 신원은 숨겨지지만 모든 거래는 공개적으로 표시됨
- 모네로, 제트캐시는 개인 정보 보호에 중점을 둔 암호화폐인데, 어떻게 함?
- Zcash는 zk-SNARK 사용함
	https://www.kaggle.com/datasets/bigquery/crypto-zcash
- 모네로는 다 숨겨버리는데, 분석이 가능한가????
	아예 새로운 암호화폐 제시: Traceable Monero: Anonymous Cryptocurrency with Enhanced Accountability [IEEE Transactions '19]
	- 모네로 보니까 역시 기술보다는 규제로 가는게 맞는 것 같은데,,,,
	익명성 해제 논문이 있네;; Deanonymizing Transactions Originating from Monero Tor Hidden Service Nodes [WWW '24]
		- 읽어보고 싶은데, 오픈된게 없음

## 아닌가, 결국 유동성 갑인 비트코인에서의 AML 탐지 방법 개선(실시간성, 설명가능성)으로 가야하나
- 규제가 어떻게 될지 모르겠지만, 모네로 같은건 빡센 규제대상이 될 것임. 그럼 사람들이 이용을 더 안하게 되겠지. 유동성은 자금세탁에 중요하니까...


## 의심 패턴 예
- 대량 소액거래: 구조화 또는 스머핑?
- 빠른 자금 이동: 자금이 여러 블록체인(예: 비트코인에서 이더리움으로)을 거쳐 알려지지 않은 출처로 이전되는 "체인 호핑" (크로스체인 거래)
- 왕복 거래: 중개자를 거쳐 원래 지갑으로 다시 보내는 자금


## 위험 점수 정량화?
- 클러스터링, 거래량, 알려진 위험한 엔터티와의 연결 등의 요소를 기반으로 지갑이나 거래에 점수 할당


## 블록체인 분석 도구의 기능
- 지갑 클러스터링: 동일한 사용자 또는 엔터티에 속하는 지갑을 그룹화합니다.
- 다크넷 마켓 추적: 불법 마켓플레이스와의 연결 감지.
- 믹서 또는 텀블러 감지: 거래 추적을 은폐하도록 설계된 서비스 식별.



decentralized exchanges (DEXs), and cross-chain bridges, Monero and Zcash

새로운 자금세탁 기술, 규제 변화 및 분석 방법

익명성 해제 (Deanonymizing)

?? 교환에서 제공되는 비트코인의 AML 점수 ??? 이거 뭐임




정리 한번 해야되는데, 뭔가 정리가 안되네
