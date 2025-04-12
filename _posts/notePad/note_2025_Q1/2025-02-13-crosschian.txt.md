---
title: "[Note] 25-02-13 crosschian.txt"
categories: [Notepad, Daily]
tags: 
date: 2025-02-13 21:49:00 +0900
comments: false
---
---

###### money laundering in a cross-chain world
I want to build a strong foundation before I start my research, so please elaborate on the technical details of cross-chain first.

※ [ELLIPTIC CROSS-CHAIN REPORT 2022]
https://www.elliptic.co/resources/state-of-cross-chain-crime-report
// 내용이 좋긴해. full-version 받을까 말까


이러한 조사 결과는 가상자산 서비스와 범죄 수사관에게 주요 위험을 초래하는 암호화폐 업계 전반에 만연한 문제인 '크로스체인 문제'의 대두를 강조합니다.
국제자금세탁방지기구(FATF)도 2022년 6월 가상자산 위험 보고서(report on virtual asset risks)에서 크로스체인 거래, 즉 '체인 호핑'을 통한 자금 세탁을 지적한 바 있습니다.

크로스체인 활동을 촉진하기 위해 익명으로 일반적으로 사용되는 세 가지 유형의 서비스
== 규제가 없고 익명으로 처리할 수 있는, 기존 거래소의 대안 (※기존 거래소는 AML/KYC 규제를 강화 중임)
1) decentralized exchanges (DEXs): 불법적으로 취득한 토큰을 "같은 블록체인"에 있는 토큰으로 전환	// what is token?
	- 동일한 블록체인에서 교차 자산 스왑을 허용하는 탈중앙화 서비스(종종 스마트 컨트랙트에서 실행됨)
2) cross-chain bridges: 한 블록체인의 불법 자산을 완전히 다른 블록체인으로 '연결' == '체인 호핑'
3) coin swap services: 계좌 개설 없이도 블록체인 내 또는 블록체인 간에 자산을 교환할 수 있는 중앙화된 익명 서비스
	


# 이더리움, 바이낸스 스마트 체인, 폴리곤, 솔라나 등의 블록체인을 기반으로 다양한 탈중앙화 금융(DeFi) 프로토콜
# NFTs, memecoins and stablecoins such as Tether.


가상 자산 서비스 제공자(VASP)
가상 자산과 법정 통화 간에 현금을 변환하는 서비스를 제공
- 중앙 집중형 거래소(CEX)는 중앙 기관 또는 조직이 운영하는 암호화폐 거래소 유형인 반면, 
- 분산형 거래소(DEX) [28] 는 분산형 네트워크에서 작동하며 사용자가 중앙 기관 또는 조직 없이 피어투피어 또는 피어투풀 방식으로 거래할 수 있도록 함
	- Curve와 Uniswap



_____________
■ Cross-chain bridge란?
다양한 ​​블록체인 간에 거래가 이루어질 수 있게 하는 소프트웨어 애플리케이션인 크로스 체인 브리지
- 일반적으로, 래핑 모델을 사용하여 블록체인 간에 토큰을 이동함


종류: RenBridge


DeFi 프로토콜과의 관계?

_____________
■ 왜 크체가 필요한가?
상호 운용성 문제
- 각 체인은 자체 규칙, 합의 메커니즘, 거버넌스 구조로 작동하여 통신이 거의 불가능합니다.
--> 이거 예시


_____________
■ 크체 거래의 기술적 메커니즘


크로스체인 거래는 직접(온체인) 및 간접(오프체인) 접근 방식 으로 분류



_____________
■ 크체를 이용한 자금세탁 예

- 체인 호핑(Chain Hopping ): 거래 흔적을 은폐하기 위해 여러 체인에 걸쳐 자금을 빠르게 이동시키는 행위
- 브리지 세탁 : 분산형 크로스 체인 브리지를 사용하여 자금을 이동시키는 것
- 래핑된 자산 믹싱(Wrapped Asset Mixing ): 불법 자산을 래핑된 토큰(예: WBTC, WETH)으로 변환
- DEX 중재 및 크로스 체인 스왑 : 여러 DEX에서 자산을 스왑하여 거래 난독화 생성

- 고급 ML 방법(예: 토큰 스왑 및 위조 토큰 생성)
- 투기자로 위장하는 것과 같은 자금 세탁 방법


스테이블코인?

개인 정보 보호 중심 브리지? Thorchain, Secret Network, Incognito

크로스 체인 브리지가 등장하기 전에 사용자는 
보관형 토큰 스왑 서비스, 중앙 집중형 거래소 및 거래 서비스를 통해 블록체인 간에 자금을 이동했습니다. 
합법적인 사업체인 이러한 조직은 AML 및 KYC 프로세스를 도입하여 범죄자가 현금화에 사용하기 어렵게 만들었습니다. 


■ DeFi 애그리게이터: 체인 호핑에 대한 더 저렴하고 편리한 대안 중 하나
 암호화폐의 익명 스와핑을 용이하게 함
여러 DEX에 걸쳐 주문을 분할 하여 거래를 최적화
1inch, Matcha, ParaSwap은 여러 DEX를 거쳐 거래를 라우팅하여 최상의 가격을 찾는 DeFi 애그리게이터
일부 애그리게이터는 사이버 범죄자가 플랫폼을 사용하지 못하도록 고객 확인(KYC) 또는 자금 세탁 방지(AML) 검증을 적극적으로 구현하고 잇음


■ DeFi 믹서: 애그리게이터와 비슷하지만 난독화를 확대
스마트 계약을 기반으로 하는 DeFi 믹서는 여러 사용자의 자금을 모아 이러한 암호화폐를 혼합하여 다른 주소에 배포


거래 수수료를 최소화하고 거래 속도를 높이려는 욕구에서 비롯된 사이버 범죄자들은 ​​
크로스 체인 세탁을 위해 사이드 체인이나 상태 채널과 같은 레이어 2 솔루션으로 전환하고 있습니다. 
[23.07. merkle science blog]


■ Types of On-Chain Privacy Technology:
Privacy Coins (e.g., Monero, Zcash): Use ring signatures, stealth addresses, and zk-SNARKs.
Mixing Services (e.g., Tornado Cash): Break transaction history using smart contracts.
Confidential Transactions (e.g., MimbleWimble): Hide transaction values and sender-receiver details.
CoinJoin & PayJoin (e.g., Samourai Wallet, Wasabi Wallet): Combine multiple transactions to obscure ownership.

_____________
■ 현재 크로스체인 블록체인 분석툴은 어느 수준까지 되는지?
Chainalysis Reactor - 기업 분석툴인 것 같은데, 어떤 기능까지 지원해주는지?


자금 세탁에 대한 온체인 분석은 두 가지 다른 서비스 그룹과 온체인 엔티티에 초점을 맞추고 있습니다

- 중개 서비스 및 지갑: personal wallets, mixers, instant exchangers, various types of DeFi protocols
- 중앙 집중형 거래소 + P2P 거래소, 도박 서비스, 암호화폐 ATM


■ 현업의 조사관들이 생각하는 한계와 필요기술?

- 일단 1세대 레거시 블록체인 분석 도구(ex. ?)는 이런걸 탐지 못함


■ 크체 분석 기술을 개발한 기업
- Elliptic
- Chainalysis?

_______________________________________________________________________________________________________________________________________________
##### 이거 조사하면서 나온 흥미로운 연구분야


■ token transparency initiatives?
사회적 문제까지 고려한 크체인가? 흥미롭구먼 이런 연구도 좋을 것 같다 


■ 크체 거래의 보안 위험
- Bridge Exploits: Ronin Bridge 해킹 사건 ($620M stolen)
	--> hostile social engineering attacks (by North Korea’s Lazarus Group)
- Fake Deposits & Reentrancy Attacks: 실제 입금 없이도 입금 증명을 조작하여 자금을 인출
- Cross-Chain MEV (Maximal Extractable Value): Front-running and sandwich attacks


■ 다크웹?
이쪽 파다보면 다크웹까지 연결될 수 있나?



블록체인 3.0까지 갔어??