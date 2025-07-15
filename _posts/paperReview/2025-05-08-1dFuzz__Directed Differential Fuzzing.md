---
title: "[FZZ] 1dFuzz: Directed Differential Fuzzing"
categories: [PaperReview, Vulnerability]
tags: [fuzzing, PoC]
date: 2025-05-08 23:15:25 +0900
comments: false
excerpt: "1dFuzz: Reproduce 1-Day Vulnerabilities with Directed Differential Fuzzing [ISSTA, 2023]"
--- 
---


---

## INTRODUCTION


- SW 취약점 방어를 위해 먼저 소스 코드 없이 패치된 1일 취약점에 대한 PoC를 재현해야 함
    - 이를 위해 1) 정확한 패치 위치 인식, 2) PoC 생성 2가지 단계가 필요

- 패치를 정확하게 인식하는 데 어려움이 있음 
    1. 컴파일 최적화로 인해 패치가 없는 동일한 코드라도 바이너리에서 많은 차이가 발생할 수 있고, 
    2. 버그 수정이 아닌 다른 유형의 코드 업데이트(예: 새로운 기능)도 바이너리에서 차이를 만들어서, 보안 패치와 분리하기 어려움


- PoC를 어떻게 만들 것인가? 일반적인 접근은 두 가지:
    1. Symbolic execution: 입력 제약 조건을 수학적으로 모델링해 도달 경로를 계산
        - 단점: 복잡한 프로그램에서는 확장성 부족 ◆
    2. Directed fuzzing: 특정 위치에 도달하도록 퍼저를 가이드
        - CFG 거리와 같은 메트릭을 활용하여 퍼저가 패치 위치에 더 가까이 접근하도록 안내
        - 단점: "취약점이 실제로 트리거되었는가?"를 감지하는 런타임 감지기(sanitizer)가 부족 (??????)

-  It will help design proper **sanitizers** if we could recognize the unique feature of patches

(Insigth = Trailing Call Sequence)
- 핵심 아이디어: 보안 패치의 "고유한 특성"은 무엇인가?
    - 같은 입력(PoC)을 주었을 때, 
        - 패치 전 바이너리는 위험 코드까지 실행되고, 
        - 패치 후 바이너리는 입력을 거부하고 조기 종료됩니다.
    - 결과적으로, 두 바이너리의 함수 호출 순서(trace)의 마지막 부분이 달라짐.  (패치 위치 이후 서로 다른 execution traces를 생성)
    - 이 차이를 Trailing Call Sequence (TCS) 라고 정의하고, 이것을 보안 패치의 고유하고 일반적인 특징으로 사용합니다.

(Contribution 3개)
- 호출 그래프를 분석하여 패치 위치(후보)를 찾는 새로운 솔루션 1dLoc ■
    - 패치된 바이너리와 패치되지 않은 바이너리의 호출 그래프를 구축
    - 두 바이너리의 함수를 align
    - 정적 분석을 통해 서로 다른 callee function sequences(즉, TCS 후보)를 가진 aligned 함수 찾기 = 패치 함수 후보 

- 1dLoc에서 인식한 패치 후보에 대한 PoC를 생성하기 위해 Directed 퍼징 사용

- 퍼징 중에 테스트 케이스가 패치를 성공적으로 트리거하는지 여부를 감지하기 위해 새로운 TCS 기반 sanitizer `1dSan` 제안 ■
    - 패치된 바이너리와 패치되지 않은 바이너리에 대해 diﬀerential 테스트를 적용하고, 
    - 테스트 중에 호출되는 두 개의 함수 시퀀스를 추적하며, 
    - 이 두 시퀀스의 마지막 N함수가 서로 다른 경우, PoC를 보고하도록 함

- 함수 호출 관계에 기반한 새로운 distance metric을 제시하여 directed fuzzing을 유도하고 효율성을 개선함 ■
    

---

## BACKGROUND AND RELATEDWORK

### 2.1 Locating Security Patches (패치 위치 식별)

- Static binary diﬃng.
    - BinDiff [2]: CFG(제어 흐름 그래프) 기반 함수 및 블록 유사도 비교
    - BinHunt [22]: 바이너리를 중간 표현(IR)으로 리프팅하여 유사도 계산
    - CoP, Esh: 심볼릭 실행 기반으로 블록간의 I/O 의존성이나 슬라이스를 비교
    - 한계점
        - 일반적인 코드 변경(새 기능 추가 등)도 차이로 나타남 → 패치인지 아닌지 구분 어려움
        - 컴파일러 최적화로 인해 실제 패치와 무관한 구조적 차이가 발생 가능
        

- Dynamic binary diﬃng.
    - 실행 중의 차이를 관찰
        - 실행 trace 비교 (Bayer et al., BinSim)
        - 시스템 콜 시퀀스 및 메모리 액세스 비교 (Blex, Wang et al.)
    - 한계점
        - 분석하려면 정확한 입력이 먼저 필요함 → 포괄성이 낮음
        - 실행 경로에 따라 차이가 발견되지 않을 수 있음 (입력에 민감)

- AI-based binary diﬃng.
    - 바이너리 함수나 블록을 벡터로 임베딩하고, 유사도 학습을 통해 패치된 부분을 식별
        - Asm2Vec, InnerEye, VulSeeker-pro 등
    - 한계점
        - 대부분 함수 수준에서 동작하여, 정확히 어떤 명령어(instruction)가 패치되었는지 알기 어려움


---

### 2.2 Generating PoCs for Patches (패치 트리거용 PoC 생성)

- Symbolic execution. (◆너무 옛날 논문들만 있는데)
    - 패치된 코드 경로까지 도달하는 입력 조건을 수식으로 모델링하여 SMT solver로 해를 구함
        - APEG [9]: conducts symbolic execution to generate PoCs (S&P’08)
        - Ma et al. [31]: directed symbolic execution to generate input that reaches speciﬁed code lines (2011)
        - Dinges and Agha [16]: propose combining symbolic backward execution and concrete forward execution to generate PoCs. (ASE’14)
    - 한계점 
        - 제약 조건이 복잡할 경우, 경로 폭발(path explosion)로 인해 대규모 소프트웨어에 확장성 부족

- Directed fuzzing.
    - SemFuzz [49] (CCS’17) : CVE 설명으로부터 의미 정보 추출
    - 1dVul [34] (DSN’19) : fuzzing + symbolic execution 혼합
    - CAFL [26] (SEC’21), BEACON [25] (S&P’22).: 효율적인 경로 탐색 구조 제안 ■
    - 한계점
        - 대부분 거리 기반 탐색만 수행함 → PoC가 패치를 실제로 트리거했는지 확인하는 기능이 부족 (sanitizer 없음)

---

### 2.3 Fuzzing (퍼징 방법)

- fuzzing은 입력을 자동으로 변형(mutate) 하여 프로그램을 테스트하고, 크래시나 예외 상황을 탐지하는 데 초점이 있음

- Coverage-Guided Fuzzing
    - AFL, LibFuzzer, Honggfuzz 등: 코드 커버리지를 기준으로 입력을 진화시킴
    - 특정 코드(예: 패치 위치)를 집중적으로 탐색하는 데 한계
- Directed Fuzzing
    - AFLGo: 타깃까지의 제어 흐름 거리 이용
    - Hawkeye, ParmeSan: 인접 함수 거리, sanitizer 정보 활용
    - 거리 계산이 정적 기준에 의존하여 실제 취약점을 정확히 겨냥하기 어려움

(이건 퍼징은 아님)
- Differential Testing
    - 서로 다른 버전의 프로그램을 동일한 입력으로 실행, 결과 차이를 분석
        - Nezha [43] records the path diﬀerences to guide fuzzing and ﬁnd semantic bugs. (DSC, 2021)
        - EXPOZZER [35] employs diversiﬁed dual execution to report memory corruption bugs (S&P’17)

---

### 2.4 Sanitizers (실행 중 취약 동작 감지)

- ASan, TSan, MSan 같은 Sanitizer는 일반적인 버그 탐지(힙 오버플로우 등 메모리 오류, 데이터 경쟁)에 초점이 맞춰져 있음

- 본 논문에서는 differential testing 메커니즘을 핵심 sanitizer 방식으로 사용


---

## 3 TCS: TRAILING CALL SEQUENCE

- Motivating Example (CVE-2020-35523)
    - 18번 줄의 정수 오버플로우로 취약점이고, 입력에 따라 tw와 w가 영향을 받음
    - 11줄의 패치는 tw와 w의 합이 단어 크기의 최대 정수보다 큰지 여부를 확인
    - 만약 그렇다면, 12줄에서 오류 처리 함수 TIFFErrorExt()를 호출하고, 15줄에서 현재 함수를 종료하여 이후의 취약한 코드 실행을 방지함
    - 이는 입력의 유효성을 확인하고, 오류를 보고한 후, 정상적인 실행 흐름을 종료하는 패치의 일반적인 작업 흐름임
        > 그러니까, 패치 방식은, 입력을 sanity check해서,  
        > 오버플로우 가능성이 있으면 에러 핸들러(TIFFErrorExt) 호출하고 함수 종료 (return)

- Definition 1: TCS Feature
    - `TCS(P, I, N)` = 프로그램 `P`에 입력 `I`를 넣었을 때, 종료 직전의 `N`개의 함수 호출

- `§3.3 Popularity of the TCS Feature`
    - “TCS는 보안 패치의 공통적이고 유일한 행동 특성이다”를 실증적으로 입증하기 위해 진행된 대규모 실험과 분석 결과를 설명하는 부분
    - 패치 사례 분류 → 통계 분석 → TCS 보편성 확인 (논문의 신뢰성을 받쳐주는 실증적 근거를 제시)
- 패치들이 코드를 어떻게 바꾸는지 코드 패턴 분류 
    - 모든 패치에 TCS feature가 있는 것은 아님
        - 모든 댕글 포인터를 NULL로 설정하는 등, 단순히 실수를 수정하는 패치도 있음 (18.7%)
    - 즉, 71.3%는 TCS feature가 있다
- 결론
    - 보안 패치의 대다수는 "입력 검증 → 실행 중단" 패턴을 따르며,
    - 이는 실행 흐름의 마지막 함수 호출들(TCS) 을 바꾸는 결과를 낳는다.
    - 즉, TCS는 보안 패치의 일반적이고 신뢰도 높은 특성이다.

> “우리는 TCS가 중요하다고 생각해요” (X)  
> “우리가 만든 기법은 전체 보안 패치의 71.3% 이상에 적용 가능한 일반화된 근거에 기반한 기법입니다” (O)  

> ◆ 이런 분석을 해야 하나...

---

## 4 DESIGN

- 4.1 Overview
    - 이 두 바이너리를 비교해, 보안 패치가 적용된 위치를 찾고
    - 해당 위치를 유도해서 PoC 입력을 만들며
    - PoC가 실제 취약점을 트리거했는지 검증까지 수행


### 4.2 Binary Code Alignment

- (전제) 소스 코드 없이 순수 바이너리만으로 작동
- (상황) 소스 코드에 약간의 변경만 있어도 패치된 바이너리와 패치되지 않은 바이너리는 크게 다를 수 있음
    - 단순히 함수 주소나 이름에 의존하지 않고, 의미적으로 동일한 함수를 식별해서 두 바이너리를 “정렬”해야 이후의 비교(패치 탐지, TCS 비교 등)가 정확하게 동작함
- (Contribution) 새로운 콜 그래프 기반 Binary Code Alignment 솔루션을 제시함. 이는 2단계로 구성됨


1. Call Graph Construction (호출 그래프 구성)
    - (문제) 바이너리 수준에서는 간접 호출(indirect call) 이 많아 정확한 호출 그래프 구성이 어렵다.
        - 예: 함수 포인터, 가상 함수 호출 등 → 정적 분석만으로는 어렵다.
    - (해결) 따라서 바이너리에서 함수 간 호출 관계를 정적으로/동적으로 수집해 Call Graph(CG) 를 만든다.
        - 1단계: 정적 분석 기반 “스케치” 생성
            - 직접 호출 관계 수집
        - 2단계: 동적 분석으로 간접 호출 보완
            - 퍼징 과정에서 실제로 호출된 간접 호출 정보 수집
        - 퍼징이 진행될수록 call graph는 점점 더 정확해짐

2. Function Alignment (함수 매칭)
    - (목표) 두 call graph(CG_pre, CG_post)에서 의미상 동일한 함수를 찾아 1:1로 매칭하기
    - (idea) 두 바이너리에서 같은 입력으로 실행한 트레이스를 기반으로 함수 대응 관계를 추정
    - (방법) 유사도 점수 기반 탐욕적 정렬(Greedy Matching)
        - 유사도 점수는 4가지로 구성됨: In-Out Degree Factor, Operand Factor, Caller Matching, History Matching
    - 함수 매칭 또한 퍼징이 진행되며 점진적으로 개선됨

> 이게, 이 논문에서 다 새롭게 제안한 방법이야? 기존에 존재하던 방법론이 아니고?  
    - 차별점은: 정적 + 동적 정보를 반복적으로 보완하며 정렬하여 점진적 개선되는 구조.
    - 즉, 퍼징과 연동되어 동적으로 향상되는 구조
> 기존의 Binary Code Alignment는 어떻게 이뤄졌나?
    1. Heuristic-based Static Matching: BinDiff, BinHunt, DiscovRE, DarunGrim → CFG가 많이 변하면 오탐 증가
    2. AI/ML 기반 Matching: Gemini (NDSS’17), DeepBinDiff (USENIX’20), VulSeeker-Pro
    3. Dynamic Trace Matching: BinSim, Blex, CoP

---

### 4.3 1dLoc: TCS-Based Patch Locator

- 1dLoc: 정적 분석 기반 패치 위치 식별
    - 앞서 만든 정렬된 호출 그래프(call graph alignment)를 활용해 보안 패치가 적용된 위치를 자동으로 식별하는 방법을 제시

- (상황) 보안 취약점 재현을 위해서는 패치된 코드가 어디에 존재하는지를 아는 것이 중요하지만,
    - (문제) 기능 추가, 리팩터링 등이 많을 때, 소스 코드가 없는 Binary 수준에서 binary diff로는 보안 패치인지 구분하기 어려움
    - (idea) 패치 위치를 TCS를 기반으로 찾자: “패치가 적용된 함수는call sequence가 바뀌었을 가능성이 높다"
        - 즉, 정렬된 함수 쌍 (f_pre, f_post)에 대해, 각 함수가 호출하는 하위 함수(callees)의 시퀀스가 다르면, 그 함수는 보안 패치가 적용된 후보일 수 있다.

- 구체적인 작동 방식 ★
    - DFS (Depth-First Search) 방식으로 Binary Code Alignment에서 얻은 MatchedSet (f_pre, f_post) 쌍을 하나씩 순회
    - 각 함수쌍에 대해 CFG를 생성하여 각 경로(path)를 따라가며 호출되는 하위 함수들(callee sequence)을 수집
    - pre/post 각 함수에서 수집한 callee 함수 시퀀스 집합을 비교하여, 한쪽에만 존재하는 시퀀스가 있다면 → 해당 함수는 패치 후보

> ◆ 약간, 시간 관계가 이상하다는 생각이 들어. (f_pre, f_post) 쌍을 퍼징을 진행하면서 점진적으로 정확해지는데, 퍼징을 하기 위한 목표지점인 패치 위치를, (f_pre, f_post) 쌍에서부터 찾아가잖아?
    - 논문은 비동기적 흐름(asynchronous refinement), iterative pipeline을 구성
    - 1dLoc은 초기에는 불완전하게 작동할 수 있지만, 퍼징이 계속됨에 따라 정확도와 효과가 향상된다

> ◆ CFG에서 경로를 추출한다는 게 어떤거지? 모든 가능한 경로를 보는건가?
    - CFG(Control Flow Graph) 생성이라 함은, 함수 내부의 분기 조건을 기준으로, basic block을 노드, 조건 분기를 엣지로 해서 표현한 것
    - CFG의 경로(path) 추출이라 함은, 다양한 분기 조합(=가능한 실행 경로)을 나열하는 것
    - 각 경로별로 호출되는 함수들(callees) 수집 = callees sequence

> ◆ "모든 가능한 경로 추출" 이게 말이 되나?
    - 많은 정적 분석 도구들(angr, CodeSurfer)이 택하는 일반적인 경로 수 제한(truncation) 전략
        - 제한된 경로 깊이(depth-limited path exploration)
        - 대표 경로만 수집
        - 경로 병합 및 추상화
    - 본 논문에서는 “우리가 수집 가능한 범위 내에서, 가능한 경로들을 정적 CFG 분석으로 뽑아보고, 호출 시퀀스를 비교하겠다”
    - 구체적으로, 본 논문은 angr 프레임워크를 사용하여 함수 단위의 CFG를 추출함
        - angr는 실행 가능 경로를 정적으로 분석할 수 있지만, 기본적으로 경로 수를 제한하는 분석 옵션을 제공함
        - 각 경로에서 호출된 callee 함수 시퀀스를 모아 set으로 추상화함
    

> ◆ 비실행 함수는 동적 trace에 안 잡히면 간접 호출 분석에서 누락될 수 있음 (????)
    - 본 논문에서는 정적 분석으로 확인할 수 없는 호출 관계는 실제로 프로그램을 실행시켜 관찰해서 call graph를 점점 보완
        - 간접 호출(함수 포인터 등)을 분석하기 위해 동적 실행 trace 를 사용
    - 즉, 특정 함수가 테스트 중 실행되지 않으면, 해당 함수에서 발생하는 간접 호출 정보는 수집되지 않음
        - 조건 분기 뒤에 숨어 있는 간접 호출 (e.g., if (x > 100) fp();)
        - 특정 입력이 아니면 호출되지 않는 예외 처리 함수
        - 전혀 사용되지 않는 패치된 코드 (예: 보안 경로 전용)

> ◆ 정적 분석으로 확인할 수 없는 호출 관계 = indirect call
        - 함수 포인터: 어떤 함수 포인터를 반환하는지는 런타임에 결정됨
        - 가상 함수 (C++ virtual call): 정확한 타입은 런타임에 결정됨
        - 인터프리터/플러그인 기반 시스템: 실행 전에 어떤 함수가 실행될지 전혀 예측 불가

> ● (이 방식의 한계점)  
- 호출 시퀀스가 같아도 다른 내부 로직(예: 조건문)만 바뀌었을 경우는 탐지 어려움  
- 너무 많은 호출이 바뀐 함수는 false positive 가능성 있음 (e.g., 대규모 리팩터링)  
- 비실행 함수는 동적 trace에 안 잡히면 간접 호출 분석에서 누락될 수 있음


---

### 4.4 1dSan: TCS-Based Sanitizer

- 1dSan: 런타임에서 TCS 차이 감지 (PoC 판단)
    - TCS (Trailing Call Sequence)"를 이용해 퍼징 중 생성된 입력이 실제로 취약점을 트리거했는지 판단하는 런타임 검증기(sanitizer)

- 구현 세부사항
    - 바이너리 실행은 QEMU 모드의 AFL++ 위에서 수행
    - 호출된 함수는 instrumentation 되어 있음
    - 각 바이너리는 마지막 N개의 함수 호출만 기록 (N=8)(실험상 최적값)
    - 호출 시퀀스는 순환 버퍼(circular buffer) 형태로 저장
    - fuzzer가 두 TCS 버퍼를 비교하여 차이 여부를 즉시 판단


---

### 4.5 Directed Diﬀerential Fuzzing

- Directed Differential Fuzzing: 패치 위치로 퍼징 유도 + 차등 실행 비교



---

##5 IMPLEMENTATION
 
- AFL++ QEMU 모드를 기반으로 함



