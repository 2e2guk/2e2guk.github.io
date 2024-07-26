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
use_math : true

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

### naive 한 접근

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

이제, 세그먼트 트리를 구현하는 방법에 대해서 살펴보자. 기본적으로, 두 가지 방법, **재귀, 비재귀** 방식의 세그먼트 트리가 있는데, 여기서는 일단 재귀 방식의 세그먼트 트리에 대해서 알아보

자. 그 전에, 재귀 방식의 세그먼트 트리를 관통하는 재귀를 어떤 식으로 사용하는지를 알아보자. 

#### "재귀" 방식의 segTree

**(현재 노드가 표현하는 구간, 목적하는 구간) 의 포함 관계와, 구간별로 무언가 저장되어 있다는 사실**을 이용한다.

"목적하는 구간" 이라는 말이 조금 애매할 수 있는데, 만약 update(i, v) 라면, [i, i] 일 것이고, query(l, r)이면, [l, r] 이 될 것이다.

가능한 경우를 생각해 보면, 다음 세 가지 중 하나이다. 

1. 현재 노드가 표현하는 구간, 목적하는 구간이 전혀 겹치치 않는다.

-> 이 경우, 재귀 호출을 중단한다. 

2. 현재 노드가 표현하는 구간, 목적하는 구간이 완벽하게 겹친다.

-> 완벽하게 겹친다는 말은, 현재 노드가 표현하는 구간이, 목적하는 구간 내에 완벽하게 포함되는 경우를 말한다. 

어? 그럼 반대로 목적하는 구간이 현재 노드가 표현하는 구간에 완벽하게 포함될 수는 없나요? 라는 질문을 할 수 있지만, **그런 경우는 불가능한 경우이다.**

이런 경우, 현재 노드가 표현하는 구간을 그대로 사용해 주면 된다. 

3. 일부만 겹치는 경우

-> 이런 경우엔, 현재 노드가 표현하는 구간을 반으로 쪼개서 다시 재귀 호출함으로써, 1번 혹은 2번, 둘 중 하나의 경우로 만든다.


#### segTree의 초기화

세그먼트 트리를 초기화해 보자. 초기화하는 방식은, 먼저 리프 노드에 입력받은 배열을 넣고, 재귀적으로 부모 노드들을 만들어 가는데, 

주의할 점은, 여기서는 **구간 합**이기 때문에, 부모 노드는, **두 자식 노드의 값의 합** 을 부모 노드의 값으로 가지면 된다. 코드를 통해 살펴보자. 

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

#### update(i, v)의 구현

위에서 설명한 방식(재귀!!)으로 업데이트를 진행해 주면 된다. 다만, 우리가 만들고자 하는 자료구조의 특성상, **어떤 leaf node의 값을 업데이트하면, 업데이트한 leaf node ~ root** 

**node 까지의 값** 이 전부 업데이트 된다는 사실을 인지하면 된다. 다음 그림에서 빨간색으로 표시된 부분은 업데이트되는 노드들이다. 

<img width="462" alt="스크린샷 2024-07-26 오후 7 51 49" src="https://github.com/user-attachments/assets/bca0c064-f97f-4d9d-b443-40a3b8baae53">

코드를 통해 살펴보자. 

```cpp
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

/// ... codes

void update(IndexType idx, ValueType newValue) {
  ValueType diff = newValue - array[idx];
  array[idx] = newValue;
  updateSegmentTree(1, 0, n - 1, idx, diff);
}
```

이렇게 구현하면 된다. 

#### query(l, r) 의 구현

마찬가지로, 위에서 설명한 방식(재귀!!!) 으로 구현하면 된다. leaf를 제외한 각 노드들은, 자신의 subtree의 구간의 합의 결과를 담고 있다. 

아래 사진에서, 주횡색으로 표현한 부분이 자신이 담고 있는 값에 대한 구간 정보이다. 

<img width="521" alt="스크린샷 2024-07-26 오후 7 53 00" src="https://github.com/user-attachments/assets/d2ccfe6f-a954-47b3-a773-e23c1136aeee">

그리고, 재귀적인 방식으로, query(2, 6) 을 호출한다고 하면, 맨 처음에는, 전 구간을 호출하고, 재귀적으로 구간을 쪼개 가며, 원하는 구간을 찾아나간다. 

아래 그림에서, 빨간색으로 표현된 구간이, 실제로 덧셈에 반영되는 구간이다.

<img width="466" alt="스크린샷 2024-07-26 오후 7 56 34" src="https://github.com/user-attachments/assets/eb403cdf-5709-49b2-94c5-1f1f1de0c3b0">

처음에 전 구간 [0, 7] 을 호출하고, 재귀적으로 반으로 쪼개 나가다가, 현재 표현하는 구간 [2, 3], [4, 5], [6] 각각은, 목표 구간인 [2, 6] 에 완벽하게 포함되기에, 더해진다. 

코드를 통해 살펴보자

```cpp
ValueType sumSegmentTree(IndexType node, IndexType left, IndexType right, IndexType start, IndexType end) {
  if (left > end || right < start) return 0;
  if (left <= start && right >= end) return segmentTree[node];
  IndexType mid = (start + end) / 2;
  return sumSegmentTree(node * 2, left, right, start, mid) + sumSegmentTree(node * 2 + 1, left, right, mid + 1, end);
}

// ... codes

ValueType query(IndexType left, IndexType right) {
  return sumSegmentTree(1, left, right, 0, n - 1);
}
```

눈여겨볼 점은, 구간이 전혀 겹치지 않는 경우, left > end || right < start 에는, 0을 반환한다는 사실이다. 여기서는, **구간 합** 을 구하고자 하는 것이기

때문에, 0을 반환하면, 0이 더해져 봤자, 우리가 구하고자 하는 값에 전혀 영향을 끼지치 못한다. 예를 들어, 곱셈인 경우에는 1이 된다. 

#### 전체 코드

이렇게, 구현한 전체 코드는 다음과 같다. 

전체 코드의 main 함수 속 내용은, [boj 2042 구간 합 구하기](https://www.acmicpc.net/problem/2042) 의 내용이다. 구현된 것은 0-based index, 

문제에서 함수를 호출할 때는 1-based index로 호출하므로 **문제의 상황에 따라서, 호출하는 방식** 을 바꾸면 된다. 

```cpp
// SegTree, 0-based index
// 쿼리개수 Q, O(QlogN)
// main 함수 사용예시는 boj 2042, 224ms AC
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

    void update(IndexType idx, ValueType newValue) {
        ValueType diff = newValue - array[idx];
        array[idx] = newValue;
        updateSegmentTree(1, 0, n - 1, idx, diff);
    }

    ValueType query(IndexType left, IndexType right) {
        return sumSegmentTree(1, left, right, 0, n - 1);
    }
};

int main() {
    ios::sync_with_stdio(false); cin.tie(nullptr);
    int n, m, k; cin >> n >> m >> k;

    vector<ll> arr(n);
    for (int i = 0; i < n; i++) cin >> arr[i];

    Seg<ll, ll> segTree(arr);

    for (int i = 0; i < m + k; i++) {
        ll order, left, right;
        cin >> order >> left >> right;
        if (order == 1) {
            segTree.update(left - 1, right);
        } else {
            cout << segTree.query(left - 1, right - 1) << '\n';
        }
    }
    return 0;
}
```

### 시간 복잡도

입력으로 길이 n인 배열, 쿼리(update, query)의 총 개수가 m개라 가정하자. 

1. 세그먼트 트리 구성 -> $O(log n)$

2. update(i, v) -> $O(log n)$, leaf ~ root까지, $log n$개의 노드 업데이트

3. query(l, r) -> $O(log n)$, 구간을 쪼개는 작업이 지배적인데, $log n$회 이상으로는 구간을 쪼개지 않는다.

그리고, 이런 작업들이 총 m개가 주어지므로, $O(m log n)$ 의 시간 복잡도로 문제를 해결할 수 있다. 

### 관련 문재

관련된 문제가 너무나도 많기에, 여기에는 기본적인 연습 문제만을 첨부한다. 

1.

 문제 : [codeforces ITMO academt pilot course segment tree - 1A](https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/A)
 
 풀이 : [sol](https://2e2guk.github.io/range-query/ITMOacademy-segTree-1A/)



2.

문제 : [boj 2042](https://www.acmicpc.net/problem/2042)

풀이 : [sol](https://github.com/2e2guk/ToolBox/blob/main/query처리/SegTree_recur.cpp) 풀아는, 1번 문제와 완벽히 동일허다. 













