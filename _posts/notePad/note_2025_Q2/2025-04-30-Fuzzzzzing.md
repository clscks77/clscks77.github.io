---
title: "[Note] 2025-04-30 Fuzzzzzing"
categories: [Notepad, Daily]
tags: [attack, fuzzing]
date: 2025-04-30 10:37:33 +0900
comments: false
excerpt: "symbolic/concolic execution 및 constraint solving"
--- 
---



코드 상에서, 어떤 entry point로부터 내가 지정한 어떤 위치까지의 경로를 지정했다고 치자. 즉, 원하는 위치에 도달가능한 branch 조건들이 정해진거지. 이렇게 경로가 주어졌을 때, 이 경로에 도달 가능한 "입력"을 생성하고 싶어. 여기서 입력이란, 일련의 byte code를 말하는거야. 그리고 지금 상황은 블랙박스 상황이고, Byte 단위로 해결해야 해. 이걸 Symbolic Execution의 SMT Solver(Z3 등)로 해결할 수 있니? 


SMT Solver가 어떤식으로 이걸 해결할 수 있는데? 계산을 오래 해야 하나? 그러니까, 경로가 딱 하나로 정해지면, 이에 도달 가능한 입력이 많이 생길 수 있을거 아니야? 그것들을 모두 찾는데 얼마의 시간이 걸릴 것 같아?


사실 이건 fuzzing을 변형해 본 연구 아이디어야. 퍼징이 input을 계속 변형해가면서 원하는 위치까지 도달 가능한 경로를 찾아가는거잖아? 거꾸로 경로를 변형해가면서 입력을 찾는거지. 원하는 위치까지 도달가능한 여러가지 경로가 있을 텐데, 그 경로 집합은 유한한 수도 있고 무한할 수도 있지. 어쨋든 그 집합 안에서 경로를 계속 선택해가면서 해당 경로로 도달가능한 입력을 하나씩 찾아보는거야. 이 아이디어에 대해 전문가적 관점에서 비판적으로 분석해봐. 





path explosion 문제
경로 조건이 non-deterministic 해질 수도 있음.



Targeted Symbolic Execution (예: Mayhem, Angr의 exploration_techniques 중 Directed Exploration)
    - angr: Python 기반 바이너리 분석 도구. 심볼릭 실행과 경로 탐색 모두 가능.

Concolic Execution + Targeted Reachability (예: SAGE, KLEE에서 목표 지점 우선 경로 선택)
    - SAGE: Microsoft의 whitebox fuzzer. 조건 기반 입력 생성.
    - KLEE: LLVM IR 상의 심볼릭 실행 도구. 소스 기반이나 가능성 있음.

FuzzGen: fuzzing seed를 생성하기 위해 static path constraint 기반으로 SMT solving 수행
    - path_group.explore(find=target_addr) 처럼 특정 주소까지 도달 가능한 입력을 생성할 수 있음

Driller (AFL + Angr): Fuzzing 도중 막히면 Angr로 의미 있는 입력 생성 (너의 아이디어는 이보다 solver 중심이라는 차이점이 있음)
    - Driller: AFL + angr를 결합한 hybrid fuzzer.



자동으로 취약점을 찾는 연구는 이미 많이 진행되고 있음 
이 이후, 실제로 exploit까지 가는 부분에 대한 자동화를 연구해야 함
- just 내 생각 and 내가 하고싶은거




