---
title: "ITMOacademy-segTree-3E"
excerpt: "코드포스 ITMO academy pilot course segTree step3 E번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-3E/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-02
last_modified_at: 2024-08-02
---

## 🦥 본문

### 문제 링크

[ITMOacademy-segTree-3E](https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/E)

### 풀이 시간

생략

### 문제 설명

길이 n짜리 배열이 있다. 그리고 이 배열은, 초기에는 전부 0으로 초기화되어 있다. 이때, 다음 두 가지 query가 주어진다. 

1 l r v : l ~ r - 1까지의 범위를, v만큼 업데이트한다. (+v)

2 i : index i 의 원소를 참조한다. 

### 문제 풀이

fenwick tree 기본 문제이다. 이렇게, 구간 업데이트, 점 쿼리(Range Update, Point Query) 가 주어지는 경우, lazy propagation 이나, fenwick tree 를 이용하면 되지만, 

fenwick tree가 구현상의 편의점과, 비트연산 기반의 준수한 속도를 제공하기에, fenwick tree를 사용하였다. 

완성된 코드는 다음과 같다. 

```cpp
// ITMO pilot course segTree pt1, step3 #4
// https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/D
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

// Fenwick Tree for Point Update and Range Query
template<typename ValueType, typename IndexType>
struct fenwick_PURQ {
    IndexType n;
    vector<ValueType> tree;
    fenwick_PURQ(IndexType n) : n(n) {
        tree.resize(n + 1, 0);  // 0으로 초기화
    }

    // Get sum from 1 to i
    ValueType sum(IndexType i) {
        ValueType ret = 0;
        for (++i; i > 0; i -= i & -i) ret += tree[i];
        return ret;
    }

    // Update the tree at index i by x
    void update(IndexType i, ValueType x) {
        for (++i; i <= n; i += i & -i) tree[i] += x;
    }

    // Get sum from l to r
    ValueType query(IndexType l, IndexType r) {
        return l > r ? 0 : sum(r) - sum(l - 1);
    }
};

// Fenwick Tree for Range Update and Point Query
template<typename ValueType, typename IndexType>
struct fenwick_RUPQ {
    fenwick_PURQ<ValueType, IndexType> f;
    fenwick_RUPQ(IndexType n) : f(fenwick_PURQ<ValueType, IndexType>(n)) {}

    // Update the range [l, r] by x
    void update(IndexType l, IndexType r, ValueType x) {
        f.update(l, x);
        f.update(r + 1, -x);
    }

    // Query the value at index i
    ValueType query(IndexType i) {
        return f.sum(i);
    }
};

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);

    int n, m; cin >> n >> m;
    fenwick_RUPQ<ll, int> fenw(n);

    while (m--) {
        int op; cin >> op;
        if (op == 1) {
            int l, r; ll v; cin >> l >> r >> v;
            fenw.update(l, r - 1, v);  
        } else {
            int idx; cin >> idx;
            cout << fenw.query(idx) << "\n";  
        }
    }
    return 0;
}
```

### 총평 

fenwick tree 기본 문제이다. fenwick tree는 이해하기에는 조금 어렵지만, 한번 이해하면, 세그먼트 트리류 문제들은 몽땅 fenwick tree로 바꾸어, 더 빠른 시간 내에 

해결이 가능하기에 익혀 두는 것이 좋다. 

제출 코드는 109ms에 AC를 받는다. 

<img width="874" alt="스크린샷 2024-08-02 오후 6 46 41" src="https://github.com/user-attachments/assets/e9fbf3c2-1283-4ca5-83b7-2999c98a05a0">




