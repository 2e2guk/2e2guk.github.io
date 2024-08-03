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

date: 2024-08-03
last_modified_at: 2024-08-03
---

### 아직 글 미완성입니다

## 🦥 본문

### 기존 세그먼트 트리의 한계

세그먼트 트리는 분명 좋은 자료구조이다, 그러나, 한계가 명확하다. 만약 다음과 같은 쿼리가 주어진다면 어떻게 해야 할까? 

**구간 l ~ r까지, +v update** 

이런 쿼리가 주어지면, 기존 세그먼트 트리에서는, **구간 길이** 만큼 업데이트 쿼리를 진행해야 하고, 기존 세그먼트 트리의 업데이트 쿼리 시간 복잡도가 **O(log N)** 이라면, 

**O(N log N)** 으로 늘어난다. 이를 효율적으로 처리하기 위해 만들어진 자료구조가 lazy propagation이다. 

### Lazy propagation

앞서 말했듯, lazy propagation **이하 lazyseg** 는, 구간 업데이트 쿼리를 처리하기 위해서 만들어진 세그먼트 트리이다. 

세그먼트 트리와 동작 방식이 크게 다르지는 않지만, lazyseg는, **lazy 배열**을 이용해서 구간 업데이트를 처리하는 시간을 단축한다. 

#### lazy 배열의 사용

기존 세그먼트 트리의 동작 방식을 상기해보자. 업데이트  쿼리에서는, 루트 -> 리프로 내려가며, 해당 위치를 업데이트했다. 쿼리(여기서는 구간 합)도 마찬가지. 

lazy 배열은, lazyseg 라는 이름에서도 알 수 있듯이, **변화를 즉각적으로 반영하지 않고, 변화를 뒤로 미루는 역할** 을 수행한다. lazyseg 에서 업데이트나, 어떤 쿼리(구간 합 등)

를 수행할 때, 반드시 노드를 방문하게 되어 있다. 이때, 해당 노드에 해당하는 lazy배열에 값이 있는지를 확인한다. 만약 있다면, 

**현재 노드가 담당하는 구간 길이 * lazy배열 값** 만큼 변화를 주고, 자신의 lazy배열 값을, 자식 노드들에게 전파한다. 그리고, 자신은 update를 했으니, 자신의 lazy값은 다시 0으로 만든다. 

그럼, 예를 들어 보자. 그림으로 아주 깔끔하게 잘 나타낸 [boj Book](https://book.acmicpc.net/ds/segment-tree-lazy-propagation)의 자료를 빌려 쓰겠다. 

<img width="1009" alt="스크린샷 2024-08-03 오후 3 44 08" src="https://github.com/user-attachments/assets/16447e73-ed41-4736-8cad-8c3f17cd5004">
















