---
title: "ITMOacademy-segTree-2C"
excerpt: "코드포스 ITMO academy pilot course segTree step2 C번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-2C/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-01
last_modified_at: 2024-08-01
---

## 🦥 본문

### 문제 링크

[ITMOacademy-segTree-2C](https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/C)

### 풀이 시간

생략

### 문제 설명

길이 n짜리 배열 arr[n] 이 주어질 때, 다음 두 쿼리를 처리하는 세그먼트 트리를 설계하라. 

1. update(i, v) -> i번 index의 원소를 v로 바꾼다.
2. query(val) -> arr[idx] >= val 을 만족하는, 최소의 idx를 반환. 만약 없다면 -1 반환.

### 문제 풀이

세그먼트 트리 응용 문제이다. 기본 세그먼트 트리는, 구간 최댓값 세그먼트 트리로 설계하고, query 함수를 조금 조작한다. 

만약, 현재 노드 기준, 왼쪽 자식 노드의 값이, query 함수의 파라미터로 주어지는 val 값보다 **크거나 같다면**, 왼쪽 서브트리에 찾는 답이 있을 가능성이 있다는 뜻이므로, 

왼쪽 서브 트리로 이동한다. 물론, 없을 수도 있다. 그러나, 오른쪽 서브 트리에는, **절대** 정답이 없으므로, 왼쪽 서브트리로 이동한다. 

만약, 현재 노드 기준, 왼쪽 자식 노드의 값이, query 함수의 파라미터로 주어지는 val 값보다 **작다면**, 왼쪽 서브트리에는 **절대** 답이 없으므로, 오른쪽 서브트리로 이동한다. 

leaf node까지 도착했지만, **leaf node의 값이 val 보다 작으면**, -1을 반환한다. 아니면, leaf node의 인덱스를 반환한다. 

코드를 통해 살펴보자

```cpp
    IndexType first_above(IndexType node, IndexType start, IndexType end, ValueType val) {
        if (start == end) {
            if (segmentTree[node] >= val) return start;
            else return (IndexType)-1;
        }
        IndexType mid = (start + end) / 2;
        if (segmentTree[node * 2] >= val) return first_above(node * 2, start, mid, val);
        else return first_above(node * 2 + 1, mid + 1, end, val);
    }

    IndexType query(ValueType val) {
        return first_above(1, 0, n - 1, val);
    }
```

update 함수는 별 특이사항 없다. 

완성된 전체 코드는 다음과 같다. 

```cpp
// ITMO pilot course segTree pt1, step2 #3
// https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/C
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
        makeSegmentTree(1, 0, n - 1);
    }

    ValueType makeSegmentTree(IndexType node, IndexType start, IndexType end) {
        if (start == end) {
            return segmentTree[node] = array[start];
        }
        IndexType mid = (start + end) / 2;
        return segmentTree[node] = max(makeSegmentTree(node * 2, start, mid), makeSegmentTree(node * 2 + 1, mid + 1, end));
    }

    void updateSegmentTree(IndexType node, IndexType start, IndexType end, IndexType idx, ValueType val) {
        if (idx < start || idx > end) return;
        if (start == end) {
            segmentTree[node] = val;
            return;
        }
        IndexType mid = (start + end) / 2;
        updateSegmentTree(node * 2, start, mid, idx, val);
        updateSegmentTree(node * 2 + 1, mid + 1, end, idx, val);
        segmentTree[node] = max(segmentTree[node * 2], segmentTree[node * 2 + 1]);
    }

    IndexType first_above(IndexType node, IndexType start, IndexType end, ValueType val) {
        if (start == end) {
            if (segmentTree[node] >= val) return start;
            else return (IndexType)-1;
        }
        IndexType mid = (start + end) / 2;
        if (segmentTree[node * 2] >= val) return first_above(node * 2, start, mid, val);
        else return first_above(node * 2 + 1, mid + 1, end, val);
    }

    void update(IndexType idx, ValueType val) {
        updateSegmentTree(1, 0, n - 1, idx, val);
    }

    IndexType query(ValueType val) {
        return first_above(1, 0, n - 1, val);
    }
};

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int n, m; cin >> n >> m;
    vector<int> arr(n);
    for(int i = 0; i < n; i++) cin >> arr[i];
    Seg<int, int> seg(arr);
    while(m--) {
        int op; cin >> op;
        if(op == 1) {
            int idx, val; cin >> idx >> val;
            seg.update(idx, val);
        } else {
            int val; cin >> val;
            cout << seg.query(val) << "\n";
        }
    }
    return 0;
}
```

### 총평 

약간 이분 탐색 느낌이 나는 응용문제였다. 정답 코드는 124ms에 AC를 받는다. 

<img width="876" alt="스크린샷 2024-08-01 오후 1 40 56" src="https://github.com/user-attachments/assets/f02a8618-59b5-465b-8d13-3fbd9884ed76">


