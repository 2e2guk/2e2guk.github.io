---
title: "ITMOacademy-segTree-3B"
excerpt: "코드포스 ITMO academy pilot course segTree step3 B번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-3B/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-01
last_modified_at: 2024-08-01
---

## 🦥 본문

### 아직 글 미완성입니다

### 문제 링크

[ITMOacademy-segTree-3B](https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/B)

### 풀이 시간

생략

### 문제 설명

### 문제 풀이

완성된 코드는 다음과 같다. 

```cpp
// ITMO pilot course segTree pt1, step3 #2
// https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/B
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

    void updateSegmentTree(IndexType node, IndexType start, IndexType end, IndexType idx, ValueType val) {
        if (idx < start || idx > end) return;
        if (start == end) {
            segmentTree[node] = val;
            return;
        }
        IndexType mid = (start + end) / 2;
        updateSegmentTree(node * 2, start, mid, idx, val);
        updateSegmentTree(node * 2 + 1, mid + 1, end, idx, val);
        segmentTree[node] = segmentTree[node * 2] + segmentTree[node * 2 + 1];
    }

    IndexType kth_1(IndexType node, IndexType start, IndexType end, IndexType k) {
        if(start == end) return start;
        IndexType mid = (start + end) / 2;
        if(segmentTree[node * 2] >= k) return kth_1(node * 2, start, mid, k);
        else return kth_1(node * 2 + 1, mid + 1, end, k - segmentTree[node * 2]);
    }

    void update(IndexType idx, ValueType val) {
        updateSegmentTree(1, 0, n - 1, idx, val);
    }

    ValueType query(IndexType k) {
        return kth_1(1, 0, n - 1, k);
    }
};

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int n; cin >> n;
    vector<int> arr(n, 1); 
    Seg<int, int> segTree(arr);
    vector<int> input(n, 0);
    vector<int> restoredarr(n); 

    for(int i = 0; i < n; i++) cin >> input[i];

    for(int i = n - 1; i >= 0; i--) {  
        int pos = segTree.query(input[i] + 1); 
        cout << pos << "\n";
        segTree.update(pos, 0);
        restoredarr[i] = n - pos;
    }

    for(const auto& ele : restoredarr) cout << ele << " "; 
    return 0; 
}
```

### 총평 

제출 코드는 108ms에 AC를 받는다. 

<img width="877" alt="스크린샷 2024-08-01 오후 9 36 04" src="https://github.com/user-attachments/assets/271535dc-e630-459b-92af-9b5983918c90">


