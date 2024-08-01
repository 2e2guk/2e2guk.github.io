---
title: "ITMOacademy-segTree-2B"
excerpt: "코드포스 ITMO academy pilot course segTree step2 B번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-2B/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-01
last_modified_at: 2024-08-01
---

## 🦥 본문

### 문제 링크

[ITMOacademy-segTree-2B](https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/B)

### 풀이 시간

생략

### 문제 설명

0, 1의 원소만으로 이루어진 배열이 주어진다. 이때, 다음 두 가지 쿼리를 수행하는 세그먼트 트리를 설계하여라. 

1. update(i, v) -> i번 인덱스의 원소를 v로 업데이트
2. query(k) -> k번째 "1" 의 위치를 반환한다, k는 0-based index 이다.

### 문제 풀이

모든 원소가 0 혹은 1로 이루어져 있고, k번째 1의 위치를 반환하는 것이므로, 이 문제를 다음과 같이 바꾸어 생각할 수 있다. 

합이 k + 1 인, 가장 왼쪽 부분의 마지막 1의 위치를 반환. 

루트 노드에는, 배열의 모든 원소 중 1의 개수가 저장되어 있다. 이 상태에서, 

루트 노드의 왼쪽 자식 노드의 값이 k보다 작은 경우, 그 말은, 왼쪽 서브 트리에는 "절대" 정답이 존재하지 않는다는 말이다. 오른쪽 서브 트리로 이동해, 왼쪽 서브트리는 모두 버린다. 

루트 노드의 왼쪽 자식 노드의 값이 k보다 크거나 같은 경우, 그 말은 왼쪽 서브트리 내에 반드시 정답이 존재한다는 말이다. 왼쪽 서브 트리로 이동해, 오른쪽 서브트리는 모두 버린다. 

그리고, 이동 후에는, 만약 **오른쪽 서브 트리**로 이동한 경우, 왼쪽 서브 트리는 모두 버렸으므로, k값을, k - segTree[node * 2] 와 같이 왼쪽 서브 트리는 모두 버린다. 

그리고, 이를 leaf node에 도달할 때까지 반복한다. 

코드를 통해 살펴보자. 

```cpp
IndexType kth_1(IndexType node, IndexType start, IndexType end, IndexType k) {
  if(start == end) return start;
  IndexType mid = (start + end) / 2;
  if(segmentTree[node * 2] >= k) return kth_1(node * 2, start, mid, k);
  else return kth_1(node * 2 + 1, mid + 1, end, k - segmentTree[node * 2]);
}

// codes...

Indextype query(IndexType k) {
  return kth_1(1, 0, n - 1, k);
}
```

그리고, update의 경우, 0이면 1로, 1이면 0으로의 변환이므로, XOR을 이용해 toggle 하면 된다. 

```cpp
if (start == end) {
  segmentTree[node] ^= 1;
  return;
}
```

완성된 코드는 다음과 같다. 

```cpp
// ITMO pilot course segTree pt1, step2 #2
// https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/B
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
        return segmentTree[node] = makeSegmentTree(node * 2, start, mid) + makeSegmentTree(node * 2 + 1, mid + 1, end);
    }

    void updateSegmentTree(IndexType node, IndexType start, IndexType end, IndexType idx) {
        if (idx < start || idx > end) return;
        if (start == end) {
            segmentTree[node] ^= 1;
            return;
        }
        IndexType mid = (start + end) / 2;
        updateSegmentTree(node * 2, start, mid, idx);
        updateSegmentTree(node * 2 + 1, mid + 1, end, idx);
        segmentTree[node] = segmentTree[node * 2] + segmentTree[node * 2 + 1];
    }

    IndexType kth_1(IndexType node, IndexType start, IndexType end, IndexType k) {
        if(start == end) return start;
        IndexType mid = (start + end) / 2;
        if(segmentTree[node * 2] >= k) return kth_1(node * 2, start, mid, k);
        else return kth_1(node * 2 + 1, mid + 1, end, k - segmentTree[node * 2]);
    }

    void update(IndexType idx) {
        updateSegmentTree(1, 0, n - 1, idx);
    }

    Indextype query(IndexType k) {
        return kth_1(1, 0, n - 1, k);
    }
};

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int n, m; cin >> n >> m;
    vector<int> arr(n);
    for(int i = 0; i < n; i++) cin >> arr[i];
    Seg<int, int> segTree(arr);
    while(m--) {
        int op, val; cin >> op >> val;
        if(op == 1) segTree.update(val);
        else cout << segTree.query(val + 1) << "\n";
    }
    return 0;
}
```

### 총평

세그먼트 트리 기본 응용인데, 좋은 테크닉이다. 

정답 코드는, 109ms에 AC를 받는다. 

<img width="876" alt="스크린샷 2024-08-01 오후 1 00 48" src="https://github.com/user-attachments/assets/f6352988-10a5-4d2f-a773-05b2ae805f99">








