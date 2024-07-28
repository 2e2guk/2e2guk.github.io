---
title: "ITMOacademy-segTree-1C"
excerpt: "ITMO academy 세그먼트 트리 1C 문제 풀이"

categories:
  - range-query
tags:
  - [seg]

permalink: /range-query/ITMOacademy-segTree-1C/

toc: true
toc_sticky: true
use_math : true

date: 2024-07-28
last_modified_at: 2024-07-28
---

## 🦥 본문

### 문제 링크

[ITMO academy segTree step 1 C번](https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/C)

### 풀이 시간

아이디어 : -1:56t + 구현 : -5:43t = -7:39t

### 문제 설명

구간별로 최솟값을 저장하며, 그 최솟값과 "동일한" 값을 가지는 원소의 개수를 세어서, 출력하는 세그먼트 트리를 설계하라

### 문제 풀이

기본적인 세그먼트 트리를 조금 변형해 풀어야 하는 문제였다. 

세그먼트 트리에, pair 형태로 두 값을 저장하는데, (minvalue, count) 이다. 첫번째 minvalue는 최솟값, 두 번째 count는 최솟값과 동일한 값을 가지는 원소의 개수이다. 

그리고, query를 진행하면서, 두 자식 노드들을 합칠 때, 다음과 같이 한다. 

편의상, 왼쪽 자식 노드에 저장된 값을 (m1, c1), 오른쪽 자식 노드에 저장된 값을 (m2, c2) 라고 하겠다.

1. m1 < m2

-> 이 경우, 부모 노드의 값은, (m1, c1)이 된다. (m2, c2)는 더이상 필요 없으니 버린다. 

2. m1 > m2

-> 이 경우, 부모 노드의 값은, (m2, c2)가 된다. (m1, c1)은 더이상 필요 없으니 버린다. 

3. m1 == m2

-> 이 경우, 부모 노드의 값은 (m1, c1 + c2)가 된다. 어차피 m1, m2의 값이 동일하므로, 둘중 무엇을 pair.first에 넣던지 상관 없다. 

  두 자식 노드의 최솟값이 동일하므로, count값을 서로 더해준다. 

이렇게, 완성된 코드는 다음과 같다. 

```cpp
// ITMO pilot course segTree step1 #3
// https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/C
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

template<typename ValueType, typename IndexType>
struct Seg {
    using PairType = pair<ValueType, IndexType>;
    vector<PairType> segmentTree;
    vector<ValueType> array;
    IndexType n;

    Seg(const vector<ValueType>& inputArray) {
        n = inputArray.size();
        array = inputArray;
        IndexType treeDepth = ceil(log2(n));
        IndexType treeSize = 1 << (treeDepth + 1);
        segmentTree.resize(treeSize);
        makeSegmentTree(0, 0, n - 1);
    }

    PairType makeSegmentTree(IndexType node, IndexType start, IndexType end) {
        if (start == end) {
            return segmentTree[node] = {array[start], 1};
        }
        IndexType mid = (start + end) / 2;
        PairType left = makeSegmentTree(node * 2 + 1, start, mid);
        PairType right = makeSegmentTree(node * 2 + 2, mid + 1, end);
        return segmentTree[node] = merge(left, right);
    }

    void updateSegmentTree(IndexType node, IndexType start, IndexType end, IndexType idx, ValueType newValue) {
        if (start == end) {
            segmentTree[node] = {newValue, 1};
        } else {
            IndexType mid = (start + end) / 2;
            if (idx <= mid) {
                updateSegmentTree(node * 2 + 1, start, mid, idx, newValue);
            } else {
                updateSegmentTree(node * 2 + 2, mid + 1, end, idx, newValue);
            }
            segmentTree[node] = merge(segmentTree[node * 2 + 1], segmentTree[node * 2 + 2]);
        }
    }

    PairType query(IndexType node, IndexType left, IndexType right, IndexType start, IndexType end) {
        if (left > end || right < start) return {numeric_limits<ValueType>::max(), 0};
        if (left <= start && right >= end) return segmentTree[node];
        IndexType mid = (start + end) / 2;
        PairType leftResult = query(node * 2 + 1, left, right, start, mid);
        PairType rightResult = query(node * 2 + 2, left, right, mid + 1, end);
        return merge(leftResult, rightResult);
    }

    void update(IndexType idx, ValueType newValue) {
        array[idx] = newValue;
        updateSegmentTree(0, 0, n - 1, idx, newValue);
    }

    PairType Query(IndexType left, IndexType right) {
        return query(0, left, right, 0, n - 1);
    }

    PairType merge(PairType left, PairType right) {
        if (left.first < right.first) return left;
        if (right.first < left.first) return right;
        return {left.first, left.second + right.second};
    }
};

int main() {
    ios::sync_with_stdio(false); cin.tie(nullptr);
    int n, m; cin >> n >> m;

    vector<ll> arr(n);
    for (int i = 0; i < n; i++) cin >> arr[i];

    Seg<ll, ll> segTree(arr);

    for (int i = 0; i < m; i++) {
        ll order, left, right;
        cin >> order >> left >> right;
        if (order == 1) {
            segTree.update(left, right);
        } else {
            auto result = segTree.Query(left, right - 1);
            cout << result.first << " " << result.second << '\n';
        }
    }
    return 0;
}
```

눈여겨볼 점은, merge() 함수를 작성해, 왼쪽, 오른쪽 자식 노드를 합치는 과정을 보면 된다. 

### 총평

세그먼트 트리의 변형 문제이다. 두 노드를 합치기 위해 새로운 함수를 작성한다는 점이 새로웠다. 정답 코드는, 202ms에 AC를 받는다.

<img width="877" alt="스크린샷 2024-07-28 오후 2 27 06" src="https://github.com/user-attachments/assets/10f30c3d-53eb-4123-bf15-78146c110c7f">






