---
title: "[Note] 2024-11-01 DY(Dolev-Yao) 퍼징.txt"
categories: [Notepad, Daily]
tags: 
date: 2024-11-01 21:49:00 +0900
comments: false
---
---

소개 및 연구 배경
	- 프로토콜 보안 테스트의 현재 과제

암호화 프로토콜 보안 테스트에 대한 2가지 주요 접근방식
A. Formal Verification (Design Level)
B. Implementation Testing (Fuzzing)

Key Challenges:
Research Problem:
	
설계 결함 vs 구현 버그

이론적 기초: Dolev-Yao attacke와 logical attack [10], [36]
	- DY 모델 기본 사항 이해
	- 공격자 역량
	- 논리적 공격이 중요한 이유
	- 현재 접근 방식의 한계

새로운 접근 방식: DY(Dolev-Yao) 퍼징 기법
	- 주요 혁신 및 구성 요소
	- 기존 문제를 해결하는 방법
	- 아키텍처 및 워크플로
	- 기존 접근 방식과의 비교

DY trace (흐름이 이상하지만)
	 - 구조 및 구성 요소
	 - 추적이 프로토콜 흐름을 나타내는 방식
	 - 공격 표현 능력
	 - 돌연변이 전략
	 - 발견된 공격의 구체적인 예

기존 도구 및 비교: TLS-Attacker와 TLS-Anvil
	 - TLS 공격자의 기능 및 제한 사항
	 - TLS-Anvil 접근 방식 및 적용 범위
	 - DY 퍼징이 이러한 것들을 어떻게 개선하는지

결과 및 향후 연구
	- 결과: 취약점 발견(총 7개, 새로운 취약점 4개), 성능 및 적용 범위 비교
	- 본 논문의 기여
	- 향후 연구 방향



소개 및 연구 배경
이론적 기초: Dolev-Yao attacke와 logical attack
새로운 접근 방식: DY(Dolev-Yao) 퍼징 기법
DY trace
기존 도구 및 비교: TLS-Attacker와 TLS-Anvil
결과 및 향후 연구



Logical Attack
- 코드 취약성보다는 프로토콜 설계 결함에 초점을 맞춘 vulnerability exploitation method
- Ex) impersonation, replay attacks, MITM, downgrade attacks

Dolev-Yao Model (= theoretical foundation)
- 설계 수준에서 암호화 프로토콜의 보안을 분석하는 데 사용되는 공식 프레임워크로, 메시지를 대수 시스템의 formal terms로 처리함
- (Messages as Terms) 메시지를 비트 문자열이 아닌 대수 용어로 표현함 →  senc(m, k)
- (Algebraic Properties) 방정식을 통해 암호화 작업이 작동하는 방식을 정의  →  sdec(senc(m, k), k) = m 
- (Black-box Cryptography) 완벽한 암호화 가정. 명시적으로 지정된 대수 속성만 사용할 수 있음. 비트 수준 암호화 공격은 고려되지 않음.
- Encryption과 같은 암호화 기본 요소가 안전하다고 가정하고 대신 프로토콜을 논리적으로 조작할 수 있는 방법에 초점을 둠

Dolev-Yao Attacker
- 통신 네트워크를 완벽하게 제어하여 메시지를 가로채고, 변경하고, 조작할 수 있는 DY 모델 내에서 정의된 theoretical adversary 모델
- Capabilities
- (Interception and Modification) 메시지를 가로채고 내용을 변경하거나 올바른 키에 접근할 수 있는 경우, 새로운 키로 다시 암호화할 수 있음
- (Message Crafting) 논리적 약점을 악용하여 가로챈 데이터를 기반으로 새로운 메시지 생성
- (Protocol Interference) 프로토콜 논리를 악용하는 중간자 공격, 재생, 사칭 공격 가능

Limitations of DY Model
- DY  model은 취약점을 조기에 식별하여 보안이 강화된 설계 프로토콜을 지원하지만
- 실제 구현의 코딩 오류로 인한 취약점을 놓칠 수 있으므로 실용적인 테스트 기술이 필요함




Seed Corpus
Mutations

맵퍼가 변환하는 간단한 실제 예시를 보여주세요

Big Picture: The DY Fuzzing Loop



"최첨단 퍼저(예: AFLNet, StateAFL, AFLnwe)와 비교하여 이러한 도구에 대한 DY 퍼징 효과를 평가함"
평가 결과(수치적)

"adversarial term-level 돌연변이를 포함한 DY 퍼징의 구조화된 접근 방식은 복잡한 취약점을 발견함"
발견한 취약점 예시

"기존의 비트 수준 퍼저가 놓치는 논리적이고 적대적인 동작을 포착하는 데 있어 그 우수성을 입증함"
포착한 것 예시

"새로운 접근 방식: DY(Dolev-Yao) 퍼징 기술"에 대한 내용을 다음과 같이 구성해줄래?

A. 핵심 구성요소
B. 아키텍처 및 흐름
C. 장점
D. 기존 방법과의 차이점




 