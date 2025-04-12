---
title: "[Note] 2024-11-26 Money_Laundary.txt"
categories: [Notepad, Daily]
tags: 
date: 2024-11-26 21:49:00 +0900
comments: false
---
---

>> 자금세탁 생태계 백그라운드


자금을 현금으로 전환할 수 있는 서비스로 옮기는 것

Intermediary services and wallets:
- personal wallets, mixers, instant exchangers, various types of DeFi protocols


중앙 집중형 거래소는 의심스럽거나 불법적인 출처에서 유입되는 자금을 동결할 수 있다는 점에서 훨씬 더 많은 통제력을 가지고 있습니다. 
그러나 DeFi 프로토콜은 일반적으로 자율적으로 실행되고, 사용자의 자금을 보관하지 않기 때문에 이러한 옵션이 없습니다.


-- 암호화폐를 통한 자금세탁 방법
* DeFi 프로토콜
* 블록체인 분석 및 포렌식
* 스테이블코인
* cryptocurrency tumblers = cryptocurrency mixer = 암호화폐 거래를 익명으로 만드는 도구
	--- 사용하는건 합법인데, 
* cross-chain bridge	---> [체인 호핑] Chain hopping via cross-chain bridges
	--- 한 블록체인에서 다른 블록체인으로 자금을 이동할 수 있도록 하는 것
* mixer			---> [믹싱] New mixer: YoMix takes over for Sinbad
	--- 23년 11월, 믹서 Sinbad에 대한 제재 및 폐쇄 --> 24년 1월, 비트코인 ​​기반 믹서 YoMix가 대체품 역할
	--- blender.io -> sinbad.io -> YoMix
	--- 신밧드 믹서의 작동 방식은 10자리 믹서 코드, 서비스 주소로 서명된 보증서, 최대 7일간의 거래 지연 등
* on-chain laundering skill
온체인 동작


* 분산형 금융(DeFi) 플랫폼
* 암호화폐 거래소
* Fiat off-ramping services
* CSAM 공급업체
* 거래소 컴플라이언스 팀
* OFAC: 미국 재무부 외국자산통제국
* 블록체인 분석가
* OTC(Over-the-Counter) 브로커	--> KYC/AML 정책을 구현하도록 조치	// KYC(Know Your Customer, 고객확인)
* 가상자산 서비스 제공자(VASP)




더 많은 중첩 서비스나 입금 주소에 걸쳐 자금 세탁 활동을 분산
더 많은 주소에 분산시키는 것은 의심스러운 활동으로 인해 어떤 입금 주소가 동결되는 영향을 줄이는 전략일 수도
결과적으로 자금 세탁 인프라를 표적으로 삼아 암호화폐 범죄를 퇴치하려면, """온체인 활동을 통한 상호 연결성"""에 대한 더 큰 주의와 이해가 필요



<프라이버시 강화 관점>
* Antinalysis
* 라이버시 지갑 서비스


<자금 추적 방해 기술 관점>
- 믹싱(Mixing),  코인조인(CoinJoin), 필링(Peeling), 체인 호핑(Chain Hopping)




TODO
불법 자금을 추적하고 범인을 식별하여 암호화폐 자금 세탁을 효과적으로 퇴치하기 위한 최첨단 도구와 기술

암호화폐의 고유한 특성, 분산된 구조 및 사용자에게 제공할 수 있는 익명성을 감안할 때 특히 중요

암호화폐 공간에 특별히 맞춤화된 고급 조사 기술을 개발해야

복잡한 블록체인 데이터를 분석하여 의심스러운 거래 패턴을 식별하고, 
잠재적인 불법 활동을 정확히 파악하고, 
심지어 미래의 위협을 예측할 수 있어야 

블록체인 거래를 실제 신원과 연결하는 수단을 제공해야

암호 시스템의 발전 및 변화, 범죄자들의 새로운 전략에 대한 적응력이 있어야

기존 기술의 한계점 분석 및 개선사항 도출

***
암호화폐 산업 이해 관계자 정리
-- 크게 3분야인가: 법 집행 기관, 규제 기관 및 암호화폐 산업
-- 내가 하는 연구의 적용 대상은 암호화폐 산업계 중에서도 ㅁㅁㅁ / 규제 기관에서 직접 모니터링 할 수도?


<-->
블록체인의 복잡성, 데이터 표준 및 전문성 부족



인공지능을 활용한 블록체인 데이터 분석에서 대한 아이디어


근래 핫한 분야인가 검사



Combating money laundering with machine learning – applicability of supervised-learning algorithms at cryptocurrency exchanges
https://www.emerald.com/insight/content/doi/10.1108/jmlc-09-2021-0106/full/html
(필요한 연구 방향을 잘 알려주네)
금융 기관이 준수해야 하는 법적 요구 사항으로 인해 동결된 거래의 이유를 설명할 수 있어야 합니다.
여러 논문에서 사용한 알고리즘 모두 좋은 성과를 발견했으며, 이는 모든 알고리즘이 사용 가능하다는 점을 강조합니다. 
어떤 경우든, 거래소가 알고리즘의 블랙박스 특성 때문에 규칙 기반 시스템을 사용하기로 한 한 가지 이유를 감안할 때, 
결정 트리는 솔루션으로서 주요 후보이며 자금 세탁을 줄입니다. 
부울 논리에 의한 시각화는 화이트박스로 분류되어 이유를 설명할 수 있게 한다.
+
실시간성이 상당히 중요하다.
범죄자들은 ​​여러 주소로 돈을 보낸 다음 거래소에서 현금화하여 빠르게 돈을 청소할 수 있습니다.
+
원래 데이터 세트에 레이블이 지정되지 않은 데이터가 포함되어 있다는 점을 감안할 때
적절한 접근 방식은 반지도 학습 모델을 수행하는 것
이에 레이블 전파( Kontonatsios et al. , 2017 )와 같은 새로운 알고리즘이 개발되었습니다.
+
다양한 평가 지표를 살펴보는 것
아마도 계산 비용과 시간 복잡도에 기반한 평가 지표를 살펴보면 더 정확한 결과를 얻을 수 있을 것
알고리즘이 거래를 분류하는 데 똑같이 뛰어나다는 점을 감안할 때, 계산 비용과 시간 복잡도 측면에서 어떤 알고리즘이 더 적은 리소스로 작업을 수행하는지 보는 것이 더 흥미로울 수도



****** 이미 관련 기업이 다 하고 있는데
- Elliptic: 영국 블록체인 분석 회사
- Chainalysis: 미국 블록체인 분석 회사
- CipherTrace(미국, 마스터카드에서 인수했네)
- Crystal Blockchain(네덜란드)


Valega


****** 이 분야에 대한 Sok 논문들
- Cryptocurrency is new vogue: a reflection on money laundering prevention (2024)
- The Satoshi laundromat: a review on the money laundering open door of Bitcoin mixers (2024)
- Exploring Security in Cryptocurrency: Challenges, Solutions, and Implications – A Systematic Literature Review (2024)
- Combating money laundering with machine learning – applicability of supervised-learning algorithms at cryptocurrency exchanges (2022)
	// SoK은 아닌데, 읽어보면 사실상 SoK임
- Machine learning techniques for anti-money laundering (AML) solutions in suspicious transaction detection: a review (2018)


-> 일단 얘네 내용 분류하기 (ex. ML 적용 방법에 대한 정리라던지, 믹서 관련 정리라던지, 자금세탁 방법에 대한 정리라던지)
-> 그리고 다 읽고 내 방향 정하기


