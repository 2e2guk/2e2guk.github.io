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

sqrt decomposition(제곱근 분할법)은, 주어진 N개의 값을, $\sqrt{N}$ 개의 그룹으로 나눈다. 

길이가 16인 배열이면 4개, 25인 배열이면 5개로 나누는 것이다. 그리고, 이렇게 나누어진 각 그룹을, **bucket** 이라고 한다. 

이 각 bucket 에는, 대푯값이 있는데, 이는 주어지는 쿼리가 어떤 종류인지에 따라서 달라진다. 만약 구간 합 쿼리가 주어진다면 구간 합일 것이고, 

구간 최소/최댓값 쿼리가 주어지면 구간 최소/최댓값을 대푯값으로 가질 것이다. 

이 대폿값이, "세그먼트 트리에서의 두 자식 노드의 부모 노드에 저장되는 값"이라고 생각하면 된다. 

예시를 들어보면, 구간 합을 대푯값으로, sqrt decompostion을 진행하면 다음과 같이 된다. 

<img width="701" alt="스크린샷 2024-10-14 오전 3 58 46" src="https://github.com/user-attachments/assets/79ba602d-e3c4-48ca-b466-32289948b86a">

### update 함수의 구현

update 함수는 어떻게 구현할 수 있을까? sqrt decomposition의 아이디어가 어렵지 않고, 세그먼트 트리와 비슷한 방식으로 작동하므로, 어렵지 않게 구현을 어떻게 할지 떠올릴 수 있다. 

해당 위치의 원소의 값을 갱신하고, **구간의 대폿값을 갱신하면 된다**

그럼, 자연스럽게 드는 의문은, **지금 index가 어떤 bucket에 속하는지 어떻게 알 거냐..** 라는 것일 텐데, 이는 그냥 간단하게, 갱신하고자 하는 위치를 pos(0-based index)라고 하면,

$pos / \sqrt{N}$ 을, bucket의 id로 사용하면 된다. 















