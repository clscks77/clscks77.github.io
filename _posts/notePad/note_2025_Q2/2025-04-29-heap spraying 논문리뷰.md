---
title: "[Note] heap spraying 논문리뷰 및 공격 공모전 아이디어(= 거의 논문 아이디어)"
categories: [Notepad, Daily]
tags: [attack, fuzzing, graph]
date: 2025-04-29 16:13:18 +0900
comments: false
excerpt: "Automated Exploitable Heap Layout Generation for Heap Overflows Through Manipulation Distance-Guided Fuzzing"
--- 
---


> Automated Exploitable Heap Layout Generation for Heap Overflows Through Manipulation Distance-Guided Fuzzing [Usenix '23]


## Abstract
- 목표: exploit 가능한 heap layout 만들기
- 기존 방법: 프로그램에서 추출한 heap primitive(메모리 할당, 해제, 복사 등의 기본 동작들)를 조합해서 메모리 상태를 조정
- 기존 연구 한계: heap primitive를 명시적이고 유연하게 사용하는 것이 어렵기 때문에 특정 유형의 프로그램 (ex. dispatcher-loop 구조를 가진 프로그램)에만 적용 가능
- 연구 목표: Primitive-free 방식으로 범용 프로그램에서도 exploitable heap layout을 생성
    > ◆ primitive 없이 어떻게 함?????
- 핵심 기술: manipulation distance라는 새로운 지표를 기반으로 한 fuzzing 기법



- manipulation distance
    - 현재 heap 상태에서 원하는 피해 객체(victim object)를 오염시킬 때까지의 "거리"를 측정하는 개념
        > ◆ 단순한 coverage-guided fuzzing을 넘어서, 메모리 상태의 "위험성"을 정량화해 fuzzing을 유도하는 방식ㄷㄷ


---

## 1 Introduction

- Heap overflow는 fuzzing의 발전에 따라 많이 발견되고 있지만, 발견된 crash가 실제 exploit 가능한지 판별하는 것은 여전히 수작업에 크게 의존함
- automated exploit generation(AEG)은 스택 오버플로우에 대해서는 어느 정도 성공했지만, heap overflow에 대해서는 여전히 한계가 있음
    - Heap Overflow Exploitation이 어려운 이유
        - overflow를 통해 function pointer를 포함하는 victim object를 오염시켜야 함
        - 이 작업은 heap layout의 정밀한 조작이 선행되어야 하는데, 프로그램 로직 및 heap allocator(glibc 등)의 복잡성으로 어려움이 있음
- 이에 대한 기존 연구 방법은 2가지
    - 모델링 기반 (ex. Maze): static analysis로 heap primitive를 모델링해서 수학적 최적화로 layout을 조작
    - Fuzzing 기반 (ex. SHRIKE, Gollum): heap primitive를 이용해 layout을 우연히 조작
- 이에 대한 저자들의 지적
    - 둘 다 heap primitive가 뚜렷하고 사용하기 쉬운 경우(인터프리터, dispatcher-loop 프로그램 등)만 제대로 동작
    - 일반 프로그램에서는 explicit primitive가 잘 보이지 않고, 로직이 복잡해서 적용이 어려움
- 따라서 본 논문에서 제안하는 바
    - Primitive-free: 별도 heap primitive 없이도 crash input을 변형해 exploitable layout을 생성하자
    - Heap layout space: byte 단위가아닌, heap chunk 단위로 레이아웃 조작을 측정하고, 이 거리(manipulation distance)를 줄이는 방향으로 fuzzer를 유도하자
    - 효율 향상 기법: 입력 바이트 중 중요한 부분만 mutation하고, seed prioritization으로 빠르게 탐색하자


---

## 2 Background

### 2.1 Concepts in Heap-based Exploitation

- To exploit a heap overflow: overflowed object를 사용하여 힙의 일부 중요한 데이터 구조(예: 함수 포인터가 포함된 객체)를 손상시킴
    - 손상된 포인터를 참조 해제(de-referencing)할 때 control flow hijacking 또는 기타 read/write 손상을 일으킬 수 있음
- vulnerable object: Overflow가 발생한? 객체
- victim object: 취약한 객체에 의해 손상되거나 덮어쓰여진 데이터 구조
- Proof-of-Concept (PoC): 프로그램 충돌을 일으킬 수 있는 특정 입력 (퍼저에 의해 생성됨)
- exploitable heap layout: 취약한 객체의 overflow 영역에 하나 이상의 victim object가 있는 특수한 heap layout
- exploitable PoC (ePoC): heap overflow를 트리거할 뿐만 아니라, heap overflow가 발생할 때 exploitable heap layout까지 구성하는 특수한 PoC
    - ePoC가 함수 포인터를 탈취하는 exploit은 아니지만, 
    - 공격자가 ePoC를 기반으로 Write-what-where primitive 또는 control flow hijacking capability를 쉽게 얻을 수 있으므로 
    - exploitable heap layout을 구축하는 것은 exploit을 개발하는 데 매우 중요함
- To exploit a heap overflow: 구체적인 방법은 보통 Heap Primitives가 포함됨
    - Heap Primitives: 특정 프로그램 입력을 통해 반복적으로 호출 가능한 malloc/free 조작 패턴
    - 보통 attacker가 heap primitive를 찾아내고, 그 조합(specific sequence)을 만들어 exploitable heap layout을 구성함



### 2.2 Heap Manager Internals

- Heap Chunk: heap memory의 기본 단위. 메타데이터와 실제 데이터 영역을 가짐. 상태는 Occupied(사용중), Freed(비어있음).
- Free Lists: 같은 크기의 freed chunk들은 동일한 free list에 연결됨
- Allocation 동작: malloc 요청이 들어오면 Lx 리스트에서 적절한 chunk를 찾아 반환
- Split Mechanism: 원하는 크기의 free chunk가 없으면 더 큰 chunk를 잘라서 제공하고, 남은 조각은 다시 적절한 free list로 이동.
- Merge Mechanism: 인접한 free chunk들을 병합해서 큰 chunk를 형성. free list의 구성도 변경.
- Heap Behavior Example: CVE-2017-11339 (exiv2) 사례
    - 634번 heap operation. 그 중 75%가 split-merge를 유발.
    -  일반 프로그램은 예상치 못한 heap state 변화를 잦게 일으킴
- SCATTER의 가정 (Heap Manager 모델링 전제): 매우 표준적인 glibc 기반 allocator를 기본 가정
    - 힙 작업이 재실행해도 항상 동일한 메모리 레이아웃을 만들어야 한다.
    - freed chunk를 재사용한다.
    - free list는 FIFO 또는 FILO로 동작한다.
    - split은 free list의 입출력 순서를 따르며, split 대상 chunk는 할당 요청보다 커야 한다.
    - 병합은 인접한 free chunk 사이에서만 발생한다.

---

## 3 Motivation

### 3.1 A Running Example

- 예제 프로그램 설명 (Listing 1)★
    - Pure Data를 처리: malloc(0xF0) 해서 데이터를 저장. 입력이 0xF0보다 크면 heap overflow 발생.
- PoC: 그냥 "pure data"만 크고 다른 블록은 없음.
    - heap overflow는 나지만 victim object가 존재하지 않아 exploit 불가능.
- ePoC: 
    - Entry 블록(EN) 세 개를 삽입: 다양한 크기의 home entries를 만들어 free chunk를 남김.
    - File Info 블록(FI)을 삽입: c_file 객체가 free chunk에 할당되도록 유도.
    - 최종 pure_data가 overflow 발생 시, c_file->names를 손상시킬 수 있게 되어 exploit 가능

- Heap Layout Transition 설명 (Fig 2 기준)★★
    - ???
- 이러한 기존 Primitive 기반 접근은 "strdup → free" 같은 간단한 heap primitive가 명확히 있어야 함
    - 함수 호출 횟수 (m4 반복 실행 횟수)나 프로그래밍 로직 제어(엔트리 개수 조정)가 고정되어 있지 않고 동적이라서 모델링이 사실상 불가능
    - 즉, heap primitive를 명시적으로 추출하고 조합하는 전략으로는 해결할 수 없음.

---

### 3.2 Technical Challenges and Insights

> 3가지 주요 기술적 도전 과제가 있음

- Challenge 1: "What is a desired exploitable layout?"
    - 기존 도구(Gollum, Maze)는 exploit 대상이 되는 victim object나 희망하는 heap layout을 사용자로부터 입력받음
    - 하지만 범용 프로그램에서는 victim object가 입력에 따라 조건적으로 생성되므로, 희망 layout을 사전에 정의하는 것이 어려움
        - 예: Listing 1의 c_file 객체는 FI 블록이 있어야만 생성됨
- Challenge 1's 솔루션
    - 정적 분석 (Static Analysis) + **동적 계측 (Dynamic Instrumentation)**을 활용하여 실제 프로그램 실행 중 victim object의 생성을 관찰하고, overflow로 접근 가능한 위치에 배치될 수 있는지를 분석
    - 이 과정을 통해 자동으로 "잠재적 exploitable layout"의 후보군을 식별
- Challenge 2: "How to improve the efficiency of the fuzzing-based approach?"
    - 기존 fuzzing 기반 도구들은 **메모리 공간 상의 거리(distance in memory space)**로 목표 객체와의 거리를 측정했는데,
    - 메모리 주소 간의 거리가 짧아도 heap 상에서는 많은 할당/해제 조작이 필요할 수 있음. 즉, memory space에서의 거리 ≠ 조작 난이도.
- Challenge 2's 솔루션
    - 특정 chunk(예: victim object)가 overflow 범위 안에 들어가기까지 필요한 heap 조작 횟수나 구조적 변화량을 나타내는 heap layout space에서의 거리 manipulation distance 제안
    - **"입력 바이트 중, heap operation에 영향을 주는 중요 바이트(critical bytes)"**를 식별하여 mutation 효율을 높임.
    - Multi-level scheduling 전략을 통해 중요한 입력 바이트에 더 집중하고, 좋은 seed를 선택하여 성공 가능성을 높이는 seed prioritization 방식 도입.
- Challenge 3: "How to model the side effects brought by complex heap behaviors?"
    - heap manager의 동작은 단순하지 않으며, 복잡한 side effect들이 존재:
        - split/merge 메커니즘: 하나의 malloc이 다른 free list에도 영향을 미칠 수 있음 (Section 2 참조).
        - early occupation 문제: 예상보다 일찍 특정 chunk가 다른 목적으로 사용되어 victim object를 배치할 수 없는 상황.
- Challenge 3's 솔루션
    - 이러한 side effect들을 정확히 모델링하고 조작 가능하게 만들기 위해:
    - 실행 중 manipulation distance를 동적으로 업데이트: 프로그램 실행 중 실제 heap 상태 변화에 따라 거리 계산을 실시간으로 갱신.
    - Overload Factor 개념 도입: early occupation 문제를 완화하기 위해, heap 공간의 과할당을 허용하여 중요한 chunk가 victim object에 의해 점유될 확률을 높임.

tip
- memory-level feedback 대신, layout-level feedback을 정의
- heap layout을 명시적으로 조작하는 것이 아니라, fuzzing을 통해 간접적으로 유도

---

## 4 Design
    
### 4.1 Identifying Victim Objects

- 공격에 사용할 수 있는 victim object(overflow 대상)를 자동으로 식별

- Layout Dependence Graph (LDG)
- Manipulation Distance 정의 및 업데이트
- Manipulation Distance-Guided Fuzzing
    - ASAN 없이 자체 체크 로직 (add_maybe_VO, check_OOB_before/after)을 통해 같은 취약점 여부를 확인.


```
### 4.2 Pinpointing Critical Input Bytes
### 4.3 Modeling Fuzzing-Based Manipulation
#### 4.3.1 Defining Basic Manipulation Distance
#### 4.3.2 Handling Early Occupation Problem
#### 4.3.3 Handling Split-Merge Mechanism
```

에바... 넘 어렵당...

---


> SCATTER의 주요 기술을 살짝 비틀거나 대체해서 다른 방향의 연구 주제를 찾아봅시다.


## Manipulation Distance를 Graph Search로 바꿔보기

- SCATTER는 heap layout을 manipulation distance로 측정하고, 이걸 줄이는 방향으로 fuzzing함
- Heap layout 조작을 그래프 탐색 문제로 변환:
    - Node: heap 상태 / Edge: malloc/free 조작
    - Goal: exploitable layout에 도달하는 최소 path 찾기 (shortest path problem)
        - A* Search, Monte Carlo Tree Search(MCTS) 같은 AI 기반 탐색 알고리즘을 도입.


연구 목표의 중요성, 핵심 기술의 당위성, 현실 가능성, 연구 난이도를 기준으로 각 연구 주제를 비판적으로 평가


### 연구 목표

- Graph 기반 Heap Layout 조작 연구 (Graph-Guided Heap Layout Manipulation)
    - explicit heap state graph를 구성하고 dynamic search 기반 조작을 시도
- SCATTER와 유사: Exploitable heap layout에 도달하는 heap 조작 경로(시퀀스)를 탐색하는 것
    - 결과적으로, Fuzzing을 유도하거나 직접 ePoC를 생성할 수 있는 "heap operation 경로" 생성



### 핵심 기술의 당위성

- Heap layout manipulation은 연속적인 상태 전이 문제로 볼 수 있음
    - heap layout은 malloc/free를 통한 discrete 상태 변화로 구성되며 (명확한 graph 모델링 가능)
    - malloc, free, split, merge 등의 동적인 상태 전이가 매우 복잡하므로 graph 기반 탐색이 더 효율적임
- 기존 distance metric은 local heuristic
    - 단순히 "거리 줄이기"만 보는 greedy 방식이기 때문에 전체 operation sequence나 최적 경로를 고려하지 않음
    - Graph Search는 전체 heap 조작 경로를 보고 "가장 가능성 높은/빠른 exploitable 경로"를 찾을 수 있음
> graph search 방식은 heap manipulation 문제에 구조적으로 잘 맞음




### 추가 고려사항

- 너무 거대한 graph가 되는 걸 방지하기 위해
    - 특정 operation 패턴만 pruning 하거나
    - 중요 heap operation만 골라 node 구성 (LDG 기반 pruning)



---

## BAGUA 논문과의 차이점

> Towards Automatic and Precise Heap Layout Manipulation for General-Purpose Programs [NDSS ‘23]


| 항목 | BAGUA | 당신이 제안한 연구 (Graph 기반 경로 탐색) |
| 그래프의 목적 | Heap **operation 간의 "dependency"**를 분석해서 primitive를 정리하는 데 씁니다. | Heap **layout 상태 간 "경로"**를 탐색해서 exploitable layout 도달 경로를 찾습니다. | 
| 최종 문제 모델링 | Integer Linear Programming (ILP) 로 heap layout을 만들 수 있는 primitive 호출 횟수를 계산합니다. | Graph Search (예: BFS, A*, etc.) 로 exploit layout으로 가는 action sequence를 찾습니다. | 
| Primitive 정의 | "하나의 primitive = 여러 heap operations 묶음" (종속성 있음) | Primitive를 정의할 수도 있지만, 본질은 heap 상태 변화 그 자체에 초점을 맞춥니다. | 
| Search 전략 | Primitive를 몇 번, 어떤 순서로 호출해야 하는지를 수학적 최적화로 푼다. | Heap 상태들을 explicit하게 그래프로 만들고 "최적 경로"를 직접 탐색(search) 한다. | 
| fuzzing 사용 여부 | 초기에는 fuzzer로 heap operations을 찾지만, 후반은 거의 symbolic solving (z3) 기반. | fuzzer와 연계하거나, search를 fuzzing guide로 바로 쓸 수 있음. |


- 문제 설정 방식과 방법론이 다름 (BAGUA를 보완하고 차별화하는 새로운 접근)
    - BAGUA: "어떤 primitive를 몇 번 호출하면 목표 layout이 만들어질까?"를 정적 모델(ILP)로 해결
    - 이 연구: "heap 상태를 node로 보고, 어떤 경로로 조작하면 목표 heap layout을 만들까?"를 동적 경로 탐색(Graph Search)으로 해결

