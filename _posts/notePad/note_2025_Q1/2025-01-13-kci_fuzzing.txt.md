---
title: "[Note] 25-01-13 kci_fuzzing.txt"
categories: [Notepad, Daily]
tags: 
date: 2025-01-13 21:49:00 +0900
comments: false
---
---

* [KIISC] 학회 회원가입 및 논문(논문지) 투고 안내:  https://kiisc.or.kr/bbs/nn/article/1115
* 논문지 투고 규정:  http://acoms.atit.co.kr:9090/kiisc/index.jsp?publisher_cd=kiisc
* Windows System Fuzzing



- 분량이 6면인가?

TODO
- 새로운 분류법으로 분류하기
- 계보 그림 그려주고 싶네 (taxonomy)
_______________________________________________________________________________________
## KIISC 좋은 논문, 문장 참고

- (AI 요약) 이 연구는 생성형 인공지능과 관련된 범죄를 기존 사이버범죄 분류법에 기초하여 세 가지 카테고리로 분류하고자 한다.
- 이에 본 연구에서는 생성형 인공지능 관련 범죄를 기존 사이버범죄 분류법에 착안하여 생성형 인공지능 침해범죄 위협, 생성형 인공지능 이용범죄 위협, 기타 인공지능 관련 위협으로 구분하고자 하였다. 또한, 범죄 및 위험에 대한 기술적 대응 방안을 인공지능 개발 단계별로 제시하여 현실성 있는 위협 대응 방안을 다루었다. 법·제도적 개선사항을 통해 생성형 인공지능 범죄에 대한 개발사의 책임과 데이터 수집방법론의 법제화 등을 제시하였다.

 
- 본 논문은 기계 학습 기반의 운영체제 핑거프린팅에 관한 최신 연구 동향을 종합적으로 검토하고, 기존 방법의한계를 극복하고자 하는 노력을 다룬다. 
또한, 운영체제 식별 분야의 응용 가능성과 연구 방향을 제시한다.


________________________________________________________________________________________

##Windows System Fuzzer


- WinAFL

- kAFL

- MS Fuzzer

- DIFUZE

- NtFuzz

- SpotFuzzer

- WinkFuzz


 _______________________________________________________________________________________
##NEED CHECK

Different is Good: Detecting the Use of Uninitialized Variables through Differential Replay (CCS 2019)

CAB-Fuzz: Practical Concolic Testing Techniques for COTS Operating Systems (Usenix Security2017)



--------- SAGE(Scalable Automated Guided Execution):
SAGE: Whitebox Fuzzing for Security Testing (2012)
	//  a whitebox fuzzer developed at Microsoft

Billions and Billions of Constraints: Whitebox Fuzz Testing in Production (2013)
	// Windows 애플리케이션에서 프로덕션에서 제약 조건 기반 화이트박스 퍼즈 테스트

--------- Microsoft Security Risk Detection: 소프트웨어의 취약점을 식별하는 클라우드 기반 퍼징 서비스
Microsoft Security Risk Detection Overview
	// MSRD 서비스, 기능 및 응용 프로그램에 대한 통찰력을 제공하는 공식 Microsoft 블로그 게시물
	// https://devblogs.microsoft.com/premier-developer/microsoft-security-risk-detection/?utm_source=chatgpt.com

--------- WinAFL
Bugs on the Windshield: Fuzzing the Windows Kernel
	// 윈도우 커널 퍼징에 WinAFL을 적용하는 방법을 논의 (2020)
	// https://research.checkpoint.com/2020/bugs-on-the-windshield-fuzzing-the-windows-kernel/?utm_source=chatgpt.com




##Kernel Fuzzing
- No Grammar, No Problem: Towards Fuzzing the Linux Kernel without System-Call Descriptions (NDSS 2023)
- SFuzz: Slice-based Fuzzing for Real-Time Operating Systems (CCS 2022)
- Demystifying the Dependency Challenge in Kernel Fuzzing (ICSE 2022)
- Semantic-Informed Driver Fuzzing Without Both the Hardware Devices and the Emulators (ICSE 2022)
- SyzScope: Revealing High-Risk Security Impacts of Fuzzer-Exposed Bugs in Linux kernel (USENIX SEC'22)
- HEALER: Relation Learning Guided Kernel Fuzzing (SOSP 2021)
- - -★ NTFUZZ: Enabling Type-Aware Kernel Fuzzing on Windows with Static Binary Analysis(S&P 2021)
- Finding Bugs in File Systems with an Extensible Fuzzing Framework (TOS 2020)
- Finding race conditions in Kernels: from fuzzing to symbolic exection (2020)
- Hydra: An Extensible Fuzzing Framework for Finding Semantic Bugs in File Systems (SOSP 2019)
- Fuzzing File Systems via Two-Dimensional Input Space Exploration (S&P 2019)
- Unicorefuzz: On the Viability of Emulation for Kernelspace Fuzzing (USENIX WOOT'19)
- Razzer: Finding Kernel Race Bugs through Fuzzing (S&P 2019)
- MoonShine: Optimizing OS Fuzzer Seed Selection with Trace Distillation (USENIX Security2018)
- FUZE: Towards Facilitating Exploit Generation for Kernel Use-After-Free Vulnerabilities (USENIX Security2018)
- - -★ kAFL: Hardware-Assisted Feedback Fuzzing for OS Kernels (Usenix Security2017)
- DIFUZE: Interface aware fuzzing for kernel drivers (CCS 2017)
- IMF: Inferred Model-based Fuzzer (CCS 2017)



---------------------------

I'm finding Windows Kernel fuzzer. these are what i found:
WinAFL, kAFL, DIFUZE, MS Fuzzer, NtFuzz, SpotFuzzer, WinkFuzz, WinFuzz, What the Fuzz, Winnie.
Check if I found it right. and Please let me know if there are any other fuzzers for the Windows kernel.


-- Syzkaller: 통과
원래 Linux용으로 개발된 Syzkaller는 커널 견고성을 테스트하기 위한 시스템 호출 시퀀스를 생성하는 데 중점을 두고 
Windows에 맞게 어느 정도 조정된 커버리지 중심의 시스템 호출 퍼저

구글이 개발한 오픈소스, 커버리지 가이드 커널 퍼저로, 원래는 리눅스용으로 설계되었지만 윈도우 커널 퍼징에 맞게 조정되었습니다
// https://research.checkpoint.com/2020/bugs-on-the-windshield-fuzzing-the-windows-kernel/?utm_source=chatgpt.com

오픈소스 노력 : 윈도우용 오픈소스 버전이 있지만 아직 초기 단계
// https://github.com/qsdj/win-syzkaller?utm_source=chatgpt.com


-- IOCTLbf
유효한 IOCTL 코드를 스캔하고 생성 기반 퍼징을 수행하여 Windows 커널 드라이버의 취약점을 발견하도록 설계된 도구입니다

// https://github.com/koutto/ioctlbf?utm_source=chatgpt.com

 ?


I'm looking for papers that cover what these fuzzers {WinAFL, MS Fuzzer, IOCTLbf, Syzkaller, What the Fuzz} do about doing Windows kernel fuzzing








IOCTLbf	2012
Syzkaller	2016?
WinAFL	2016
kAFL	2017
DIFUZE	2017
MS Fuzzer	2019
NtFuzz	2021
What the Fuzz	2021
SpotFuzzer	2022
WinkFuzz	2023
WinFuzz	2023












