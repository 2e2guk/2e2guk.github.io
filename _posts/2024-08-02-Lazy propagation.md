---
title: "Lazy propagation"
excerpt: "느리게 갱신되는 세그먼트 트리 설명"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/Lazy propagation/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-02
last_modified_at: 2024-08-02
---

### 아직 글 미완성입니다.

## 🦥 본문

### 기존 세그먼트 트리의 한계

세그먼트 트리는 분명 좋은 자료구조이다, 그러나, 한계가 명확하다. 만약 다음과 같은 쿼리가 주어진다면 어떻게 해야 할까? 

**구간 l ~ r까지, +v update** 

이러면, 기존 세그먼트 트리에서는, **구간 길이** 만큼 업데이트 쿼리를 진행해야 하고, 기존 세그먼트 트리의 업데이트 쿼리 시간 복잡도가 **O(log N)** 이라면, 

**O(N log N)** 으로 늘어난다. 이를 효율적으로 처리하기 위해 만들어진 자료구조가 lazy propagation이다. 

### Lazy propagation


