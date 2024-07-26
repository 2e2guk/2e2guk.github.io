---
title: "segment-tree"
excerpt: "segment tree 자료구조 설명"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/segment-tree/

toc: true
toc_sticky: true
use_math = true

date: 2024-07-26
last_modified_at: 2024-07-26
---

## 🦥 본문

### 아직 글 미완성입니다

### segment tree 의 등장 배경

세그먼트 트리는, 다른 말로는 구간 트리 라고도 하는데, 등장하게 된 배경은 다음과 같다. 

예를 들어서, 당신이 어떤 웹사이트를 소유하고 있고, 이 웹사이트의 가입자 수를, 2010년 ~ 2024년, 년도별로 추적하고자 한다고 하자. 

그런데, 웹사이트의 규모가 커서, 당신은 각 년도별로 가입자 수를 조사하기 위해 여러 명의 사람을 고용했다고 하자. 

그러면, 당신이 고용한 사람들은, 예를 들어서 2010년에 190명의 사람들이 가입한 사실을 찾았다면, 당신에게 (2010년, 190명) 의 형태로 

조사 결과를 보내 줄 것이다.

그리고, 이 글 작성 기준인 2024년 기준, 지난 년도들은, 한해동안 가입한 사람의 수가 이미 정해져 있지만, 2024년은 아직 끝나지 않았으므로, 

6월에 50명, 7월에 70명, 8월에 몇명 ... 이런 식으로 계속해서 조사 결과가 들어올 것이다. 

참고로, 당신이 고용한 사람들은, 이미 조사가 완료된 사람들은 더 이상 조사하지 않는다. 다시 말해서, 중복이 발생하지 않는다. 

그리고, 당신의 상사는 당신에게, "XX씨, XXXX년 ~ XXXX년 가입자 수 총합이 몇이지?" 와 같은 사실을 질문한다고 하자. 

이때, 이 문제를 해결하기 위해 효과적인 자료구조를 설계해 보자. 

#### 문제 단순화하기

이 문제를 단순화해 보면, 다음과 같이 단순화할 수 있다. 

n개의 원소가 있는 배열 a[n]이 있다, 이 배열에 대해. 다음과 같은 두 연산을 정의한다. 

1. update(i, v) = i번 index의 원소 값(a[i]) 를, v로 바꾼다.
2. query(l, r) = 구간 [l, r] 의 합, $\sum_{i = l}^r a[i]$ 을 구한다.

이렇게 정의된 두 연산을 효율적으로 처리하는 자료구조를 **Segment Tree** 라고 한다. 

### naive 한 접급

이 문제를, naive 하게 접근해 보자. 반복문을 많이 사용하면, 해결할 수 있을 것이다. 

입력 배열 a[i] 만 유지하고, 

update(i, v) 는, a[i] 의 값을 단순히 v로 바꾸는 작업을 수행하면 되고, 이에는 $O(1)$ 의 시간이 걸린다.

query(l, r) 은, 단순히 index l ~ index r까지의 구간 합을 구하면 될 것이다. 이에는, $O(n)$ 의 시간이 걸린다. 

그리고, 이 두 작업은 얼마나 주어질지 모르기 때문에, 이 두 작업이 총 m개 주어진다면, $O(nm)$ 의 시간 복잡도가 걸린다고 볼 수 있다. 

n이나 m이 커짐에 따라서, 매우 비효율적으로 동작한다. 이를 최적화해 보자. 

### Segment tree의 설계

배열 [3, 1, 2, 5, 6, 4, 3, 2] 가 주어진다고 가정하자. 

세그먼트 트리는, **binary tree(이진 트리)** 의 형태를 하고 있으며, leaf node에는, 주어진 배열의 원소들이 들어가며, 정의된 구간 연산, 

query(l, r) 이, 구간 **합** 이므로, 이들의 **부모 노드에는, 두 자식 노드의 합** 을 저장한다. 그러면, root에는, 전 구간의 총합이 들어가게 되는 것이다. 

query(l, r) 은, 구간의 합일수도, 구간 내의 원소의 개수, 등등, 문제에서 정의하는 내용에 따라서 달라지며, 이 글에서는 구간 합이 query(l, r) 로 주어진 경우를 다룬다. 

주어진 배열로, 세그먼트 트리를 구성하면, 다음과 같다. 

<img width="484" alt="스크린샷 2024-07-26 오후 7 16 46" src="https://github.com/user-attachments/assets/8b4ac2b4-2cf0-4155-99ec-09c34c65e0c6">

포화 이진 트리의 형태를 가지고 있는데, 이 이유는, 주어진 배열의 원소의 개수인 n이, **2의 거듭제곱** 형태이기 떄문이다. 만약, n이 2의 거듭제곱 형태가 아니라면, 

n보다 큰 2의 거듭제곱 수 중 가장 작은 거듭제곱수로 n을 바꾸고,n값을 바꿈에 따라 추가로 생긴 원소는, **구간 합이 query(l, r)로 주어지는 경우, 0** 으로 채워 넣는다. 

만약 구간의 곱이면, 1이 들어가면 된다. 

#### segTree의 초기화

세그먼트 트리를 초기화해 보자. 

```cpp
    vector<ValueType> segmentTree;
    vector<ValueType> array;
    IndexType n;

    Seg(const vector<ValueType>& inputArray) {
        n = inputArray.size();
        array = inputArray;
        IndexType treeDepth = ceil(log2(n));
        IndexType treeSize = 1 << (treeDepth + 1);
        segmentTree.resize(treeSize);
        makeSegmentTree(1, 0, n - 1);
    }

    ValueType makeSegmentTree(IndexType node, IndexType start, IndexType end) {
        if (start == end) {
            return segmentTree[node] = array[start];
        }
        IndexType mid = (start + end) / 2;
        return segmentTree[node] = makeSegmentTree(node * 2, start, mid) + makeSegmentTree(node * 2 + 1, mid + 1, end);
    }

```

이렇게 









