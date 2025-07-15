---
title: "[Note] 2025-05-07 공모전 GraphSearch 전략 구체화를 위한 스터디"
categories: [Notepad, Daily]
tags: [attack, fuzzing]
date: 2025-05-07 00:32:06 +0900
comments: false
excerpt: "Graph-Guided 1-day 취약점 PoC 생성 전략 구체화,를 위한 스터디"
--- 
---


- 함께한 GPT1: <https://chatgpt.com/c/68123257-3d3c-800b-8bda-6c64f8257742>
- 함께한 GPT2: <https://chatgpt.com/c/681b793b-0e4c-800b-bb4d-cae9172622d0>

---


## 대략적인 아이디어

- Directed Differential Fuzzing보다 효율적인 1-day 취약점 PoC 생성 전략
    - Call Graph, Control Flow Graph 기반의 Graph Search → Vulnerable Point까지의 경로 탐색
    - Symbolic Execution의 SMT Solver(Z3 등) 기반의 도달 조건을 만족하는 input byte (PoC) 생성 



## 지금 문제점을 정리해보면

- ...... Directed 퍼저 공부부터 해야겠는데


## 퍼저 공부..

- 소스 코드 기반의 정적 분석, 경로 조건 해석
    - 이걸 디컴파일 코드 또는 원본 소스코드를 대상으로 하면 White-Box 상황
    - 바이너리 or 계측된 실행 파일을 대상으로 하면 Gray-Box 상황

- KLEE, SAGE, Driller 등이 소스 코드나 디컴파일 결과에서 CFG를 분석하고 symbolic execution 수행
    - 이런건 White-Box 상황
    - 조건문을 해석하고 symbolic path constraints를 만들고, SMT solver로 유효한 입력을 계산함
        - 흠,,, 그럼 지금 할려는 것도 White-Box로 가야한? 디컴파일 햇?

- corpus pruning


---

## GPT의 실현 가능성을 높이기 위한 제안

- 경로를 먼저 수집하는 정적 분석기 + SMT Solver 파이프라인
    - 예: Angr로 find() + path.condition 추출 → Z3로 입력 생성
- 탐색 우선순위 큐 기반의 경로 관리
    - 경로 조건 간 우선순위를 도달 가능성, 간결성, 목표 근접도 기반으로 평가
- 의미 있는 경로 조건만 선택 (e.g., input에 의존하는 조건만)
    - 부적절한 경로를 제외해서 Solver 시간 절감




## 관련 연구분야 키워드 뽑아보면

- Path Constraints Fuzzing

- JIGSAW: Efficient and Scalable Path Constraints Fuzzing [S&P 2022]
    - SymFusion: Hybrid Instrumentation for Concolic Execution [IEEE/ACM International Conference on Automated Software Engineering, 2022]
    - Marco: A Stochastic Asynchronous Concolic Explorer [ICSE, 2024]

- static path constraint fuzz
- Targeted Symbolic Execution
- Concolic Execution + Targeted Reachability



## 경로&퍼징 관련 논문

- **Driller: Augmenting fuzzing through selective symbolic execution [NDSS, 2016]**
    - 잠재적 버그를 발견하는 현재 기술에는 정적, 동적, 그리고 콘콜릭 분석 시스템이 있으며, 각각 장단점을 가지고 있습니다. 
    - 취약점을 유발하는 입력을 생성하도록 설계된 시스템의 일반적인 한계는 얕은 버그만 발견하고 실행 파일에서 더 깊은 경로를 탐색하는 데 어려움을 겪는다는 것입니다. 
    - 퍼징은 애플리케이션의 구획을 분석하는 데 사용하고, Concolic Execution은 구획을 구분하는 복잡한 검사를 충족하는 입력을 생성하는 데 사용
    - 두 기법의 장점을 결합하여 각각의 약점을 완화하고, 컨콜릭 분석에 내재된 경로 폭발(path explosion)과 퍼징의 불완전성을 방지

- **Angora: Efficient Fuzzing by Principled Search [S&P, 2018]** ★
    - 심볼릭 실행 기반 퍼저는 양질의 입력을 생성하지만 속도가 느리고, 무작위 변이 기반 퍼저는 실행 속도가 빠르지만 양질의 입력을 생성하는 데 어려움을 겪음
    - Angora의 주요 목표는 **심볼릭 실행 없이 path constraint를 해결**하여 branch coverage를 늘리는 것
        > path constraint가 뭔데
    - path constraints를 해결하기 위해 도입한 핵심 기법들: scalable byte-level taint tracking, context-sensitive branch count, search based on gradient descent, input length exploration.

- **BEACON: Directed Grey-Box Fuzzing with Provable Path Pruning [S&P, 2022]** ★★
    - Unlike coverage-based fuzzing that gives equal attention to every part of a code, 
        - directed fuzzing aims to direct a fuzzer to a **specific target** in the code, (e.g., the code with potential vulnerabilities)
        - 하지만 기존의 Directed fuzzer들은 코드에 도달할 수 없는 많은 프로그램 경로를 상징적 또는 구체적으로 실행하기 때문에 여전히 효율적이지 않음
    - 증명 가능한 방식으로 수많은 경로에서 그레이박스 퍼저를 효과적으로 유도할 수 있는 BEACON을 제시
        - 대상에 도달하기 위한 추상화된 전제 조건을 계산하는 경량 정적 분석의 도움을 받아, 실행 경로의 82.94%를 런타임에 제거(prune)할 수 있으며, 
        - 분석 오버헤드는 무시할 수 있을 정도(<5시간)임
    - 제거된 경로는 대상과 관련하여 반드시 스퓨리어스(spurious)여야 함
    
- **SelectFuzz: Efficient Directed Fuzzing with Selective Path Exploration [S&P, 2023]** 
    -  SelectFuzz는 대상 코드와의 제어 및 데이터 종속성을 각각 포착하는 두 가지 유형의 관련 코드, 즉 경로 발산 코드와 데이터 종속 코드를 식별
        - 이후 관련 코드 블록만 선택적으로 계측하고 탐색
    - 다양한 프로그램 경로와 입력의 도달 확률을 정확하게 측정하는 새로운 거리 지표를 제안

- **DDGF: Dynamic Directed Greybox Fuzzing with Path Profiling [ISSTA, 2024]**


- **Hawkeye: Towards a Desired Directed Grey-box Fuzzer [CCS, 2018]** (● 1dfuzz 계열인가?)
    - 목적지(예: 버그 위치, patch diff 등)를 정확히 지정한 fuzzing 수행
    - distance-guided mutation + coverage feedback
    - Directed 그레이박스 퍼저의 네 가지 바람직한 특성
    - 새로운 정적 분석을 통해 Hawkeye는 호출 그래프, 함수, 대상까지의 기본 블록 레벨 거리와 같은 정보를 정확하게 수집

- **Directed Greybox Fuzzing (AFLGo) [CCS, 2017]**
    - 최초의 “directed grey-box fuzzing” 개념화
    - **목표 함수(target functions)**까지의 "distance"를 계산하고, 이를 퍼징의 피드백 지표로 활용
  


---

## 퍼징 관련 논문

- SDFuzz: Target States Driven Directed Fuzzing [USENIX, 2024]
    - an efficient Directed Fuzzing tool driven by Target States

- K-LEAK: Towards Automating the Generation of Multi-Step Infoleak Exploits against the Linux Kernel [NDSS 2024]
    - to facilitate generating memory-error-based infoleak exploits in the Linux kernel

- Automatic Heap Layout Manipulation for Exploitation [27th USENIX, 2018]

- FuzzGen: Automatic Fuzzer Generation
    - 전체 시스템 분석을 활용하여 라이브러리의 인터페이스를 추론하고 해당 라이브러리에 특화된 퍼저를 합성

- Data Coverage for Guided Fuzzing [Usenix, 2024]
    - Awesome CS 논문 of 2024



## 특정 취약점 유형 대상 퍼저

- GhostRace: Exploiting and Mitigating Speculative Race Conditions [USENIX, 2024]
    - RaceCondition 타겟으로 하는 퍼징 논문도 있네 미친자들 

- DDRace: Finding Concurrency UAF Vulnerabilities in Linux Drivers with Directed Fuzzing [Usenix, 2023]



## 기타 관련 논문

- Heap Vulnerability Exploitation Techniques on the Linux Platform: An Overview [IEEE International Conference on Signal and Image Processing (ICSIP), 2024]
    - Linux에서 heap exploitation 기법의 연구 현황을 종합적으로 분석
    - 힙 메모리 관리 메커니즘, 힙 vulnerability 유형, 힙 vulnerability 발견, exploitation이라는 네 가지 측면


- AutoPwn: Artifact-Assisted Heap Exploit Generation for CTF PWN Competitions [IEEE Transactions on Information Forensics and Security 2023]
    - 기존 솔루션 = 수동으로 작성된 패턴을 기반으로 익스플로잇을 생성하는 자동화 시스템
    - 공개된 공격 아티팩트가 힙 익스플로잇에 대한 중요한 통찰력을 제공한다는 관찰 결과를 활용
    - 알려진 힙 익스플로잇 아티팩트에서 익스플로잇 패턴을 자동으로 요약하고 이를 사용하여 새로운 프로그램의 익스플로잇을 유도하는 최초의 아티팩트 기반 AEG 시스템인 AutoPwn을 개발함
    - 새로운 익스플로잇 상태 머신(ESM)을 사용하여 익스플로잇 패턴을 모델링하고, 익스플로잇의 동적 모니터링과 텍스트 설명의 의미 분석을 결합하여 효율적으로 구축하는 방안을 제안

---


## 개인연구 관련 ◆

- Learning with Semantics: Towards a Semantics-Aware Routing Anomaly Detection System [USENIX, 2024]
    - BGP 시맨틱을 통합하여 인터넷의 각 자율 시스템(AS)의 고유한 속성(라우팅 역할로 정의됨)을 정확하게 학습하는 것
    - BEAM이라는 새로운 네트워크 표현 학습 모델을 중심으로 하는 라우팅 이상 탐지 시스템을 제시
    - BEAM을 적용한 라우팅 이상 탐지는 새로운 경로 공지를 관찰하여 예상치 못한 라우팅 역할 변동을 발견하는 것으로 축소됨


## AI 보안

- AI Psychiatry: Forensic Investigation of Deep Learning Networks in Memory Images [USENIX, 2024]
    - AiP라는 새로운 메모리 포렌식 기법: 고유한 배포 모델을 자동으로 복구하고 조사를 위해 실험실 환경에 다시 호스팅
    - Online learning이 배포된 모델에 대한 은밀한 공격을 위한 여러 경로를 제공한다고
    - 이렇게 공격받은 모델의 매개변수를 다시 복구하는 방법


---

# 기타 키워드

- angr, 바이너리 자동분석 툴(자동 취약점 분석) <https://hackyboiz.github.io/2021/07/10/j0ker/angr_part1/>

- 최근 3년간 CFI 우회 공격 연구 동향 타임라인


- 이쪽도 퍼징쪽인가보네: ASE, FSE, ICSE


- LLM 관련 Awesome CS 논문 (2024)
    - Don't Listen To Me: Understanding and Exploring Jailbreak Prompts of Large Language Models [Usenix, 2024]
    - Adversarial Illusions in Multi-Modal Embeddings [Usenix, 2024]
    - "I Don't Know If We're Doing Good. I Don't Know If We're Doing Bad": Investigating How Practitioners Scope, Motivate, and Conduct Privacy Work When Developing AI Products [Usenix, 2024]
    - LMSanitator: Defending Prompt-Tuning Against Task-Agnostic Backdoors [NDSS, 2024]