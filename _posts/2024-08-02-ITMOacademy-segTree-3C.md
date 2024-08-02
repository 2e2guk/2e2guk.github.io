---
title: "ITMOacademy-segTree-3C"
excerpt: "코드포스 ITMO academy pilot course segTree step3 C번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-3C/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-02
last_modified_at: 2024-08-02
---

## 🦥 본문

### 문제 링크

[ITMOacademy-segTree-3C](https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/C)

### 풀이 시간

생략

### 문제 설명

길이 2n짜리 수열이 주어진다. 그리고, 각 수열은 1 ~ n 사이의 수가 정확히 두 번 등장한다. 이는 보장되어 있다. 

두 번 등장하는 수는, [처음 등장하는 위치, 마지막으로 등장하는 위치] 의 segment 를 구성하는데, 

어떤 segment 가 다른 segment 에 "Nested" 되었다는 것은, 다음을 의미한다. 

segment [x1, x2], [y1, y2] 가 있을 때, 

[x1, ... , y1, ... , y2, ... , x2] 와 같이 분포되는, 곧, **하나의 segment가 다른 segment에 완벽하게 포함되는 경우**,

포함된 segment는, 포함하는 segment에 "nested" 되었다고 한다. 

### 문제 풀이

세그먼트 트리를 응용해 풀 수 있다. 

길이 2n, 모든 원소가 **0으로 초기화된** 배열을 가지고 세그먼트 트리를 만든다ㅏ.

길이 2n짜리 배열을 왼 -> 오 방향으로 탐색하며 다음 과정을 반복한다. 

1. 첫 번째 등장하는 어떤 수 x에 대해서는, x의 첫 등장 위치만을 기억한다.
2. x가 두 번째 등장하는 순간, 1번 과정에서 저장한 x의 첫 등장 위치 ~ 두 번째로 등장한 x의 위치까지의 구간 합을 구한다.
3. 1번 과정에서 저장한 x의 첫 등장 위치에, +1 update를 진행한다.

이 과정을 반복한다. 약간 이전에 자료구조 시간에 배웠던, 스택을 이용한 올바른 괄호 문자열 찾기 문제와도 비슷한데, 

우리는 이 길이 2n 짜리 배열을, 한 번만 훑으며, 정확히 n개의 쿼리만 날려서 해결이 가능하다. 고로 시간 복잡도는 **O(NlogN)**. 

어떻게? 문제의 입력 예시를 가지고 설명해 보자. 

5
5 1 2 2 3 1 3 4 5 4

1 번째 원소인 5 -> 처음 등장했으므로, 5의 처음 등장 위치 = 1

2 번째 원소인 1 -> 처음 등장했으므로, 1의 처음 등장 위치 = 2

3 번째 원소인 2 -> 처음 등장했으므로, 2의 처음 등장 위치 = 3

4 번째 원소인 2 -> 두 번째 등장이므로, 구간 3 ~ 4 구간합 = 0 , 고로 segment 2 사이에는 어떤 segment 도 nested 되어 있지 않다. 그리고, seg.update(3, 1) 수행.

5 번째 원소인 3 -> 처음 등장했으므로, 3의 처음 등장 위치 = 5

6 번쨰 원소인 1 -> 두 번째 등장이므로, 구간 1 ~ 6 구간합 = 1, 고로, segment 1 사이에는 1 개의 segment 가 nested 되어 있다. 그리고, seg.update(2, 1) 수행. 

7 번째 원소인 3 -> 두 번째 등장이므로, 구간 5 ~ 7 구간합 = 0, 고로, segment 3 사이에는 어떤 segment 도 nested 되어 있지 않다. 그리고, seg.update(5, 1) 수행.

8 번째 원소인 4 -> 처음 등장했으므로, 4의 처음 등장 위치 = 8

9 번째 원소인 5 -> 두 번째 등장이므로, 구간 1 ~ 9 구간합 = 3, 고로, segment 5 사이에는 3 개의 segment 가 nested 되어 있다. 그리고 seg,update(1, 1) 수행.

10번째 원소인 4 -> 두 번째 등장이므로, 구간 8 ~ 10 구간합 = 0, 고로, segment 4 사이에는 어떤 segment도 nested 되어 있지 않다. 

이렇게, 한 번의 탐색만으로 모든 segment 의 nested 되어 있는 segment의 개수를 찾을 수 있었다. 

그럼, 이해를 돕기 위해, 일반화를 시도해 보자. 

#### 왜 leaf node가 전부 0으로 초기화된 segemnt tree를 사용하는가? 

우리에게 중요한 것은, "몇 개" 등장하는가 이기 때문이다. 

#### 왜 처음 등장 위치에 update +1 을 하는 거지? 

x1, y1, x2, y2 와 같이 배치되어 있는 경우, segment x가 segment y에 nested 되어 있다고 할 수 있을까? 없다. 왜? x1 의 위치 때문이다. 

y1, x1, x2, y2 와 같이 배치되어 있는 경우, segment x가 segment y에 nested 되어 있다고 할 수 있을까? 있다. 

구간 합을 구하기 전에, 이미 +1 업데이트가 되어 있다는 말은, **현재 보고 있는 segment 내에 이미 update된 segment가 포함되어 있다는 말** 이다. 

고로, 처음 등장 위치에 업데이트를 하는 것이다. 

#### 왜 query -> update 순서인가?

어떤 원소에 대해, 두 번째로 이 원소를 만났다고 생각해 보자. 그럼 우리는, 

**이 원소의 시작 위치 ~ 이 원소의 끝 위치 까지의 구간 합을 구하기** 혹은, **이 원소의 시작 위치에 +1 update** 둘 다를 해야 하는데, 

만약 update를 하고 구간합을 구하면, **자기 자신도 포함되어 버리는** 문제가 발생한다. 

구현된 코드는 다음과 같다. 

```cpp
// ITMO pilot course segTree pt1, step3 #3
// https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/C
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

    for(int i = 0; i < 2 * n; i++) {
        // 1-based -> 0-based indexing
        cin >> a[i]; a[i]--;
    }
    for(int i = 0; i < 2 * n; i++) {
        count[a[i]]++;
        if(count[a[i]] == 1) left[a[i]] = i;
        if(count[a[i]] == 2) {
            res[a[i]] = seg.query(left[a[i]], i);
            seg.update(left[a[i]], 1);
        }
    }
    for(int i = 0; i < n; i++) cout << res[i] << " ";
    return 0;
}
```

cin >> a[i]; a[i]-- 를 하는 이유는, 구현해둔 세그먼트 트리는 0-based index인데, 문제에 주어지는 배열은 1-based index 이기 때문이다. 

### 총평

흥미로운 세그먼트 트리 응용 문제였다. 응용될 방안이 다양한 문제라 생각한다. 

제출 코드는 186ms에 AC를 받는다. 

<img width="877" alt="스크린샷 2024-08-02 오후 3 34 08" src="https://github.com/user-attachments/assets/0e65a83b-b16f-4f31-992d-79b147146653">






