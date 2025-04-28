---
title: "[Study] Heap Feng Shui"
categories: [Study, Security]
tags: [attack, heap]
date: 2025-04-28 00:00:21 +0900
comments: false
excerpt: "Heap Feng Shui (힙 풍수)에 대해 알아보자"
--- 
---




**Heap Feng Shui**는 프로세스 메모리 내의 **힙 영역(heap layout)**을 공격자가 의도한 형태로 조작해서,  
이후 발생할 수 있는 **메모리 버그(Use-After-Free, Heap Overflow 등)**를 이용해  
제어권 탈취(Control Hijacking) 또는 **보안 우회(Security Bypass)**를 가능하게 만드는 heap memory arrangement 기술  

> 즉, 메모리 배치(memory layout) 컨트롤 기술이다. 직접 공격(payload execution)이 아니라 사전 준비(preparation) 기술이다.


과정
- 환경 분석: allocator 정책, 청크 크기, free-list 재사용 방식 파악
- Heap Grooming: 조정된 크기와 순서로 다수의 메모리 할당/해제를 수행해서 heap 공간을 원하는 상태로 준비
- Spray: 공격자가 제어할 수 있는 데이터를 다량 할당해서 특정 영역을 채운다
- Trigger: Use-After-Free, overflow 등 실제 메모리 버그를 유발해서 공격 성공



Heap Feng Shui에서 사용하는 주요 기술
1. Controlled Allocations
    - 크기(size)와 순서(sequence)를 조절해 특정 bin/freelist에 청크를 의도적으로 배치한다.

2. Strategic Freeing
    - 특정 청크를 free시켜서 "holes(빈 공간)"을 만든다.
    - 나중에 이 hole에 공격자가 만든 fake object나 스프레이된 청크가 재할당되게 유도한다.

3. Heap Spraying
    - 대량의 할당을 통해 heap에 공격자가 제어하는 데이터로 채운다.
    - 이때는 스프레이 패턴(반복된 구조, NOP sled, fake vtable 등)을 신경 쓴다.

4. Metadata Abuse
    - allocator metadata (예: size field, pointer fields)를 조작해 free-list 구조를 변조하거나, 부적절한 청크 재사용을 유발.










