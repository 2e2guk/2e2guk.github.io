---
title: "ITMOacademy-segTree-1B"
excerpt: "코드포스 ITMO academy pilot course 세그먼트 트리 step1 B번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-1B/

toc: true
toc_sticky: true
use_math : true

date: 2024-07-28
last_modified_at: 2024-07-28
---

## 🦥 본문

### 문제 링크

[ITMO academy segTree 1B](https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/B)

### 풀이 시간

아이디어 : -0:25t + 구현 : -1:27t = -1:52t

### 문제 설명

입력 배열이 주어지고, 이 배열의 특정 인덱스의 값을 업데이트하고, 주어진 구간의 최솟값을 반환하는 세그먼트 트리를 설계하라. 

### 문제 풀이

세그먼트 트리의 기본 문제이다. [여기](https://2e2guk.github.io/range-query/segment-tree/#segment-tree의-설계) 에서, query(l, r)로

무엇이 주어질지 모른다고 했었다. 그리고, 우리는 문제에서 정의한 query 함수에 맞게, 적절히 코드를 수정해야 한다. 

이 문제에서는, 구간의 최솟값을 요구하기 때문에, **부모 노드에는, 자신의 자식들의 값 중 더 작은 값** 을 저장하면 된다. 

완성된 코드는 다음과 같다. 

```cpp
// ITMO pilot course segTree step1 #2
// https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/B
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

template<typename ValueType, typename IndexType>
struct Seg {
    vector<ValueType> segmentTree;
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

    ValueType makeSegmentTree(IndexType node, IndexType start, IndexType end) {
        if (start == end) {
            return segmentTree[node] = array[start];
        }
        IndexType mid = (start + end) / 2;
        return segmentTree[node] = min(makeSegmentTree(node * 2 + 1, start, mid), makeSegmentTree(node * 2 + 2, mid + 1, end));
    }

    void updateSegmentTree(IndexType node, IndexType start, IndexType end, IndexType idx, ValueType newValue) {
        if (start == end) {
            segmentTree[node] = newValue;
        } else {
            IndexType mid = (start + end) / 2;
            if (idx <= mid) {
                updateSegmentTree(node * 2 + 1, start, mid, idx, newValue);
            } else {
                updateSegmentTree(node * 2 + 2, mid + 1, end, idx, newValue);
            }
            segmentTree[node] = min(segmentTree[node * 2 + 1], segmentTree[node * 2 + 2]);
        }
    }

    ValueType query(IndexType node, IndexType left, IndexType right, IndexType start, IndexType end) {
        if (left > end || right < start) return numeric_limits<ValueType>::max();
        if (left <= start && right >= end) return segmentTree[node];
        IndexType mid = (start + end) / 2;
        return min(query(node * 2 + 1, left, right, start, mid), query(node * 2 + 2, left, right, mid + 1, end));
    }

    void update(IndexType idx, ValueType newValue) {
        array[idx] = newValue;
        updateSegmentTree(0, 0, n - 1, idx, newValue);
    }

    ValueType Query(IndexType left, IndexType right) {
        return query(0, left, right, 0, n - 1);
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
            cout << segTree.Query(left, right - 1) << '\n';
        }
    }
    return 0;
}
```

눈여겨볼 점은, 여기서는 현재 노드가 표현하는 구간이 목적 범위를 벗어나는 경우, numeric_limits<Valuetype>::max() 를 반환하게 해, 

min 연산의 결과에 영향을 미치지 못하게 한다는 것이다. 

### 총평

마찬가지로 세그먼트 트리 기본 문제이다. 정답 코드는 187ms에 AC를 받는다. 

<img width="875" alt="스크린샷 2024-07-28 오전 10 30 23" src="https://github.com/user-attachments/assets/f6dd3ed0-0399-43ea-ad48-3f8042520d5a">








