---
title: "Sqrt_decomposition"
excerpt: "제곱근 분할법 설명"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/Sqrt_decomposition/

toc: true
toc_sticky: true
use_math : true

date: 2024-10-14
last_modified_at: 2024-10-14
---

## 🦥 본문

### 익숙한 쿼리

구간 쿼리의 대표적인 쿼리인, 다음과 같은 쿼리가 주어진다고 해 보자. 

1. update(u, i) -> u번 값을, i로 업데이트

2. query(l, r) -> [l, r] 구간 합 구하기

물론, 우리는 이런 쿼리가 주어진다면, 세그먼트 트리를 써야겠다고 생각할 수 있지만, 조금 다른, 어쩌면 더 직관적인 방식을 살펴보자. 

### sqrt decomposition

sqrt decomposition(제곱근 분할법)은, 주어진 N개의 값을, $\sqrt_{N}$ 개의 













