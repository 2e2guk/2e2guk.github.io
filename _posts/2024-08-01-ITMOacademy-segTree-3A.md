---
title: "ITMOacademy-segTree-3A"
excerpt: "코드포스 ITMO academy pilot course segTree step3 A번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-3A/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-01
last_modified_at: 2024-08-01
---

## 🦥 본문

### 문제 링크

[ITMOacademy-segTree-3A](https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/A)

### 풀이 시간

생략

### 문제 설명

문제 길이가 매우 짧으므로, 문제를 그냥 첨부한다. 

Given a permutation 𝑝𝑖 of 𝑛 elements, find for each 𝑖 the number of 𝑗 such that 𝑗 < 𝑖 and 𝑝𝑗 > 𝑝𝑖.

n개의 원소로 이루어진 어떤 임의의 순열 p가 주어질 때, p의 각 index i에 대해, j < i 이고, p[j] > p[i] 인 j의 개수를 찾아라. 

### 문제 풀이

세그먼트 트리를 응용해 풀 수 있다. 일단, 길이 n짜리, 모든 원소가 0으로 초기화된 배열로 세그먼트 트리를 만들고, 

다음 과정을 반복한다. 

1. [p[i], n] 의 부분 합을 구해 출력한다
2. p[i] 위치의 값에, 1을 더한다.

기본적으로, 세그먼트 트리의 leaf node에는 무엇이 담길까? leaf node는, 입력 배열 그 자체를 의미한다. 여기서는, leaf node에, 

**leaf node의 원소의 index에 해당하는 값이 등장한 횟수** 를 담는다. 

1번 과정의 의미를 알기 위해서는, 2번 과정의 의미를 이해해야 한다. 2번 과정의 의미는, p[i] 위치의 값의 수가, 이전과 비교해서 +1 번 더 등장했다는 의미이다. 

그리고 1번 과정은, 문제의 j < i 이고, p[j] > p[i] 인 원소의 개수를 찾는 과정이다. 

**등장 순서대로 update를 진행하고, update 전에 구간 합을 구하기에**, j < i 이어야 하는 문제는 해결했다. 

남은 것은, p[j] > p[i] 인 것을 어떻게 구하냐.. 인데, 기본적으로, leaf node의 index는, 1-based index로 바꿔 생각하면, **1 ~ n 까지가 저장**되어 있다. 

이때, **[p[i], n]의 구간 합을 구하면**, p[i] 업데이트 이전에 구간 합을 구하기에, p[i]의 변화를 반영하지 않고, 다시 말해, p[i]의 이전에 등장한 값들 중, 

p[i] 보다 큰 값이 몇 개인가.. 를 구할 수 있게 된다. **세그먼트 트리의 leaf node에 1 ~ n 의 값이 순서대로 저장** 되어 있다는 사실을 잘 생각해 보자. 

무슨 말인지 잘 이해가 안 될 수도 있으니, 그림을 통해서 살펴 보자. 문제의 test case의 i = 2일 때까지만 나타냈다. 

네모는, 세그먼트 트리의 leaf node를 나타낸 것이며, 처음 세그먼틑 트리를 초기화할땐 index가 0으로 시작하지만, i = 0일 때는, 편의를 위해서 세그먼트 트리의 

indexing 방식을 **1-based** 로 바꾸었다. 

![IMG_5617](https://github.com/user-attachments/assets/82e1cf32-afd3-4874-b949-7d9861ee7a85)

완성된 코드는, 다음과 같다. 

```cpp
// ITMO pilot course segTree pt1, step3 #1
// https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/A
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
    vector<int> arr(n, 0);

    Seg<int, int> segTree(arr);

    for (int i = 0; i < n; i++) {
        // 1-based indexing -> 0 based indexing
        int val; cin >> val; val--;
        cout << segTree.query(val, n - 1) << " ";
        segTree.update(val, 1);
    }
    return 0;
}
```

update 함수는, +1 을 더하는 형태로 이루어지므로, diff를 1로 주고, segmentTree[node] += diff; 이런 식으로 구현한다. 

### 총평

꽤 흥미로웠던 세그먼트 트리 응용 문제였다. 제출 코드는, 93ms 에 AC를 받는다. 

<img width="876" alt="스크린샷 2024-08-01 오후 8 24 55" src="https://github.com/user-attachments/assets/6ccc751f-6c6c-42d5-9607-173d92615fff">








