---
title: "[Note] 2024-08-17 attack_paper_search.txt"
categories: [Notepad, Daily]
tags: 
date: 2024-08-17 21:49:00 +0900
comments: false
---
---


## 8/16 금, 미팅 후
- 연구 계획 짜가기 (1. RCE 공격 논문 / 2. 자금세탁 AML)
	1. RCE 공격 논문
	   - CVE 공격/구현 논문 survey
	2. 자금세탁 AML
	   - 제안서 읽고 연구 키워드 뽑아보기
--> 밀라 setup ppt 물어보기


- RCE는 과정은 됐고, 결과만 보고하라고 하심
- 근데 RCE가 급한데, 이걸 계획하고 있는게 맞는겨??
	- 26809 분석도 8월 안에 분석 방법론 배워야하고 (여기 집중해)
	- 24500 버퍼 오버플로우 구현도 8월말까지 보여줘야하고 (이건 해도 그만, 안해도 그만이라고 했음)
--> 일단 RCE 백그라운드부터 다시....


## TODO
- AML 키워드 뽑아두기 -> RCE 끝나고, 12월부터 셋업시작하겠다 (done)
- 공격논문 선행연구 리스트업 -> 주당 하나씩, 주말동안 리뷰하겠다 (리스트 뽑기 어려운데,,,,)
- RCE 키워드 뽑기 -> RCE CVE 중에서 PoC 및 분석글 공개된걸로다가 읽어보기

NextGen Mirth Connect / CVE-2023-37679 불완전 패치 -> CVE-2023-43208
	XStream
CVE-2024-23108

# 분석 굿	
https://www.horizon3.ai/category/attack-blogs/?filter=true&page=4


# attack review
# vulnerability attack
# cve attack
# PoC
# exploit

Cybersecurity for autonomous vehicles: Review of attacks and defense	#공방 리뷰
On Security of TrustZone-M-Based IoT Systems		# 시스템 보안
Boosting Symbolic Execution for Heap-based Vulnerability Detection and Exploit Generation	#탐지


## attack 논문들 키워드
- 탐지 모델 개발
- 완화, 보호 방법 제시
- 공격 표면 탐색
- 취약점 조사
- 새로운 공격 유형 개발
- 새로운 프로토콜에 대한 공격 시도



IEEE TRANSACTIONS ON INFORMATION FORENSICS AND SECURITY


코드 주입을 위한 스택 기반 버퍼 오버플로(BOF) 공격, 
리턴 지향 프로그래밍(ROP; return-oriented programming) 공격, 
힙 기반 BOF 공격, 
포맷 스트링 공격, 
비보안 호출 가능(NSC; nonsecure callable) 함수에 대한 공격



publish or perish
혜경이가 올려준 AI 어쩌구 사이트 https://www.scopus.com/search/form.uri?display=basic#scopus-ai
논문 찾기 ai


=================================================================
0603


RtlpLogHeapFailure: 힙 손상 검사 함수

HeapAlloc, HeapFree

프리텍스팅(pretexting)


## primitive가 원시 타입(primitive type)의 primitive였나?
- 고정된 저장 공간을 차지하는 데이터

########
정리하면서..

######## Windows 10의 힙 관리 메커니즘...

## LFH (Low Fragmentation Heap) = 힙 관리 정책
- 힙 할당 해제를 반복하다 보면 빵꾸들이 들쭉날쭉 생김. 이게 힙 단편화(Heap Fragmentation)
- 솔1: Heap Chunk 그리고 Free List (bins management)방식
- 솔2: LFH (Low Fragmentation Heap) (=단편화 현상이 적은 힙)
	- 정해진 사이즈별로 Chunk를 미리 만들어 두고, 필요한 메모리에 맞는 최소한의 세그먼트를 할당하는 방식
- front-end heap manager가 작은 사이즈 Chunk들을 LFH로 관리
  back-end heap manager가 큰 사이즈 Chunk들을 Free List 방식으로 관리

- Look-Aside List도 나오네

##winDBG / LFH 유무
dt _HEAP [Heap Address]
!heap -x [Chunk Address]
	- 오프셋 0x0d6, FrontEndHeapType 필드값이 0이면 백엔드 할당자가 처리, 1이면 LOOKASIDE, 2면 LFH




## BAADF00D = 채움 패턴
- 디버깅 모드에서 힙관리자가 자동으로 채워주나봄
- BAADF00D = 할당만 받은 상태
- FEEEFEEE = 해제(free)한 상태


=======================================================

1. vftable 위치 ---> 주소가 낮다
2. 취약 버퍼 위에 있는 데이터들 정체 파악
---> 힙 메타데이터 같은데, structure를 못찾겠네. 
	- Segment heap인지, NT heap인지 구분하고 
		-> winDbg에서는 NT라고 나옴. 크기에 따라 달라지는 것 같은데, 4GB됐을 때는 어떻게 될지?
	- 쓸 수 있는 포인터 있는지, 오버플로 했을 때 거기까지 도달 가능한지 확인
3. 다른 블로그도 읽어보기
	- 파란거 아직 안읽어봄. 읽어봤는데 좀 안맞는듯. 넘어렵당


=======================================================



"예약된 메모리"는 가상 메모리 관리 시스템의 일부
아직 실제 메모리에 커밋되지 않은 가상 주소 공간의 예약된 메모리. 향후 사용을 위한 자리 표시자임.
메모리 손상을 방지하고 필요한 경우 힙 확장을 허용하는 데 도움이 되는 버퍼 영역.
	- 힙 확장을 허용하는 데 도움이 되는???
	- 우리가 확장시킬 수 있나???
ACCESS_VIOLATION이 발생합니다. 왜냐하면 실제로 사용하기 위해 할당된 것이 아니고 단지 예약된 것이기 때문

특정 Windows API를 사용하여 메모리 할당을 조작할 수도 있다
	- ?! api 뭐 -> virtualAlloc.....;;


32비트 시스템에서 포인터는 4바이트입니다.
64비트 시스템에서 포인터는 8바이트입니다.





















