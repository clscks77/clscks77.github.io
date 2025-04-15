---
title: "[Note] 2025-04-15 aml_research_goal.txt"
categories: [Notepad, Daily]
tags: [research, llm]
date: 2025-04-15 21:12:44 +0900
comments: false
--- 
---


-ref: AMLworld 합성 데이터셋 <https://chatgpt.com/c/67e4c065-7510-800b-8809-5ee3ad58c487>

## 좀더 연구 목적과 목표를 확실히 정의하고 들어가야
---
### Problem Define
- 시간 기반의 전개, 역할 분산, 다중 계좌 등의 탐지는 기존의 정형 패턴 중심의 탐지로는 어려움
	- "단계적 전개”, “의도적 분산”, “타이밍 조작” 등
- 실제 범죄자는 static하지 않기 때문에 문제를 LLM으로 address(?)해야 한다
	---> 이게 좀 이상하지...?
- 실제 False Negive 사례를 보면, 시간적인 전략이 들어갈 경우 탐지를 못하더라
	- (그 논문 내용에서 내가 눈여겨 본것)


### Insight
- LLM의 generative power를 "전략성"에 연결하여 시간적으로 구성된 계획, 행동의 시퀀스, 상호 연계된 의도를 담아보자
	- "정량화하는 기준", "전략적 행동 전개의 정의", "hallucination을 걸러낼 방법", “현실성-전략성 통제”에 관한 새로운 아키텍처를 제시


### Solution




