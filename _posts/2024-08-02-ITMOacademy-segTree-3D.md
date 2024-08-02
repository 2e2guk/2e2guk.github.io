---
title: "ITMOacademy-segTree-3D"
excerpt: "코드포스 ITMO academy pilot course segTree step3 D번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-3D/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-02
last_modified_at: 2024-08-02
---

## 🦥 본문

### 문제 링크

[ITMOacademy-segTree-3D](https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/D)

### 풀이 시간

생략

### 문제 설명

[ITMOacademy-segTree-3C](https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/C) 문제에서 이어지니, **이 문제를 읽고 오는 것을 추천**한다. 

이번에는, "nested" 가 아닌, "intersect" 이다. 

x y y x 와 같이, nested 된 segment는, intersect하지 않으며, x y x y 혹은, y x y x 와 같은 것을, intersect 라고 한다. 

### 문제 풀이

풀이는 매우 간단하다. 생각해 보면, segment 사이의 위치 관계는 딱 두 가지다, **nested(포함), intersect(교차)**. 고로, 우리는

**구하는 segment의 길이(양끝 제외) - (nested된 segment의 개수 * 2)** 를 하면 된다. 

#### 왜 (nested된 segment의 개수 * 2) 를 빼는가? 

nested 된 segment의 개수 * 2 는, **nested된 segment 들의 모든 원소의 개수** 를 의미한다. 당연하게도, 현재 구하는 segment에서 양 끝을 제외한 모든 원소에서, 

nested 된 segment를 구성하는 모든 원소의 개수를 제외하면, intersect되어 있는 원소의 개수가 나올 것이다. 

구현된 코드는 다음과 같다. 

```cpp
// ITMO pilot course segTree pt1, step3 #4
// https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/D
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

    // 인덱스 idx의 값을, diff만큼 업데이트한다. 
    void updateSegmentTree(IndexType node, IndexType start, IndexType end, IndexType idx, ValueType diff) {
        if (idx < start || idx > end) return;
        segmentTree[node] += diff;
        if (start != end) {
            IndexType mid = (start + end) / 2;
            updateSegmentTree(node * 2, start, mid, idx, diff);
            updateSegmentTree(node * 2 + 1, mid + 1, end, idx, diff);
        }
    }

    ValueType sumSegmentTree(IndexType node, IndexType left, IndexType right, IndexType start, IndexType end) {
        if (left > end || right < start) return 0;
        if (left <= start && right >= end) return segmentTree[node];
        IndexType mid = (start + end) / 2;
        return sumSegmentTree(node * 2, left, right, start, mid) + sumSegmentTree(node * 2 + 1, left, right, mid + 1, end);
    }

    void update(IndexType idx, ValueType diff) {
        updateSegmentTree(1, 0, n - 1, idx, diff);
    }

    ValueType query(IndexType left, IndexType right) {
        return sumSegmentTree(1, left, right, 0, n - 1);
    }
};

int main() {
    ios::sync_with_stdio(false); cin.tie(nullptr);
    int n; cin >> n;
    vector<int> a(2 * n, 0), count(n, 0), left(n, 0), res(n, 0);
    Seg<int, int> seg(a);
    int nested = 0;

    for(int i = 0; i < 2 * n; i++) {
        // 1-based -> 0-based indexing
        cin >> a[i]; a[i]--;
    }
    for(int i = 0; i < 2 * n; i++) {
        count[a[i]]++;
        if(count[a[i]] == 1) left[a[i]] = i;
        if(count[a[i]] == 2) {
            nested = seg.query(left[a[i]], i);
            res[a[i]] = (i - left[a[i]] - 1) - 2 * nested;
            seg.update(left[a[i]], 1);
        }
    }
    for(int i = 0; i < n; i++) cout << res[i] << " ";
    return 0;
}
```

### 총평

이것도 앞선 문제와 마찬가지로 흥미로웠다. 

정답 코드는, 124ms에 AC를 받는다. 

<img width="877" alt="스크린샷 2024-08-02 오후 4 23 07" src="https://github.com/user-attachments/assets/20bf36f4-6b5c-4513-8e1c-51a48b1ae82b">




