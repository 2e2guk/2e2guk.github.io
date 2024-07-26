---
title: "ITMOacademy-segTree-1A"
excerpt: "코드포스 ITMO academy pilot course segTree step1 A번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-1A/

toc: true
toc_sticky: true
use_math : true

date: 2024-07-26
last_modified_at: 2024-07-26
---

## 🦥 본문

### 문제 링크

[ITMO academy segTree step 1 A번](https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/A)

### 풀이 시간

아이디어 : -1:02t + 구현 : -1:12t = -2:14t

### 문제 설명

다음 작업을 수행하는 세그먼트 트리를 구현하여라. 

n, m : n은 배열의 원소의 개수, m은 구하고자 하는 쿼리의 개수. 이후 m개 줄에, 다음과 같은 쿼리 정보가 주어진다. 

1 i v : i번째 index의 원소를, v로 업데이트한다. 
2 l r : 주어지는 배열의, 구간 [l, r) 의 합을 출력한다. 

입력은, 0-based index로 주어진다. 

### 풀이 설명

가장 기본적인 세그먼트 트리 구현 문제이며, 이와 비슷하게, [boj 2042 구간 합 구하기](https://www.acmicpc.net/problem/2042) 문제가 있다. 

boj 2042 문제는, 1-based index 라는 점이 차이점이다. 이미, 팀노트에 이전에 구현해 둔 세그먼트 트리가 있었기에, 구현에 걸리는 시간을 매우 단축시킬 수 있었다. 

완성된 코드는 다음과 같다. 

재귀 방식의 segTree, 비재귀 방식의 segTree, Fenwick Tree 이렇게 3가지로 나누어서 구현했다.

#### 재귀 Seg

```cpp
// ITMO pilot course segTree #1
// https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/A
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
            cout << segTree.query(left, right - 1) << '\n';
        }
    }
    return 0;
}
```

#### 비재귀 Seg

```cpp
// ITMO pilot course segTree #1
// https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/A
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
template<typename NodeType, typename F_Merge>
struct SegTree { // 1-indexed
public:
	SegTree() : SegTree(0, NodeType()) {}
	explicit SegTree(int n, const NodeType& e) : n(n), lg(Log2(n)), sz(1 << lg), e(e), tree(sz << 1, e) {}
	void Set(int i, const NodeType& val) { tree[--i | sz] = val; }
	void Init() { for (int i = sz - 1; i; i--) tree[i] = M(tree[i << 1], tree[i << 1 | 1]); }
    // update = i번 노드의 값을 val로 설정
	void Update(int i, const NodeType& val) {
		tree[--i |= sz] = val;
		while (i >>= 1) tree[i] = M(tree[i << 1], tree[i << 1 | 1]);
	}
	NodeType Query(int l, int r) const {
		NodeType L = e, R = e;
		for (--l |= sz, --r |= sz; l <= r; l >>= 1, r >>= 1) {
			if (l & 1) L = M(L, tree[l++]);
			if (~r & 1) R = M(tree[r--], R);
		}
		return M(L, R);
	}
private:
	const int n, lg, sz; const NodeType e;
	vector<NodeType> tree;
	F_Merge M;
	static int Log2(int n) {
		int ret = 0;
		while (n > 1 << ret) ret++;
		return ret;
	}
};

// F_merge -> 자료형 적절히 수정
struct F_merge {
	ll operator() (const ll& a, const ll& b) const {
		return a + b;
	}
};
int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
	int n, m; cin >> n >> m;
	SegTree<ll, F_merge> ST(n, 0);
	for (int i = 1; i <= n; i++) {
		ll t; cin >> t;
		ST.Set(i, t);
	}
	ST.Init();
	while (m--) {
		ll t, a, b; cin >> t >> a >> b;
		if (t & 1) ST.Update(a + 1, b);
		else cout << ST.Query(a + 1, b) << '\n';
	}
    return 0;
}
```

#### Fenwick Tree

```cpp
// ITMO pilot course segTree #1
// https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/A
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

template<typename ValueType, typename IndexType>
struct fenwick_PURQ {  
    IndexType n;  
    vector<ValueType> tree;  
    fenwick_PURQ(IndexType n) : n(n) { tree.resize(n + 1); }  

    ValueType sum(IndexType i) {  
        ValueType ret = 0;  
        for (; i; i -= i & -i) ret += tree[i];  
        return ret;  
    }  

    void update(IndexType i, ValueType x) { for (i++; i <= n; i += i & -i) tree[i] += x; }  

    ValueType query(IndexType l, IndexType r) { return l > r ? 0 : sum(r + 1) - sum(l); }  
};

template<typename ValueType, typename IndexType>
struct fenwick_RUPQ {  
    fenwick_PURQ<ValueType, IndexType> f;  
    fenwick_RUPQ(IndexType n) : f(fenwick_PURQ<ValueType, IndexType>(n + 1)) {}  

    void update(IndexType l, IndexType r, ValueType x) { f.update(l, x), f.update(r + 1, -x); }  

    ValueType query(IndexType i) { return f.query(0, i); }  
};

template<typename ValueType, typename IndexType>
struct fenwick_RURQ {  
    IndexType N;  
    fenwick_PURQ<ValueType, IndexType> A, B;  
    fenwick_RURQ(IndexType N) : N(N), A(fenwick_PURQ<ValueType, IndexType>(N + 1)), B(fenwick_PURQ<ValueType, IndexType>(N + 1)) {}  

    void update(IndexType L, IndexType R, ValueType d) {  
        A.update(L, d);  
        A.update(R + 1, -d);  

        B.update(L, (-L + 1) * d);  
        B.update(R + 1, R * d);  
    }  

    ValueType query(IndexType L, IndexType R) {  
        ValueType R_Value = A.query(0, R) * R + B.query(0, R);  
        ValueType L_Value = 0;  
        if (L > 0) {  
            L_Value = A.query(0, L - 1) * (L - 1) + B.query(0, L - 1);  
        }  
        return R_Value - L_Value;  
    }  
}; 
int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int n, m; cin >> n >> m;
    // point update, range query
    fenwick_PURQ<ll, ll> fenw(n);  
    vector<ll> a(n, 0);
    for (int i = 0; i < n; i++) {
        cin >> a[i];
        fenw.update(i, a[i]);
    }
    for (int i = 0; i < m; i++) {  
        ll x; cin >> x;
        if (x == 1) {  
            ll idx, val; cin >> idx >> val;
            ll diff = val - a[idx];
            a[idx] = val;
            fenw.update(idx, diff);  
        } else {  
            ll l, r; cin >> l >> r;
            if(l > r) swap(l, r);
            cout << fenw.query(l, r - 1) << "\n";  
        }  
    }  
    return 0;
}
```

### 총평

매우 자주 사용되는, segTree의 기본 문제이다. 정답 코드는 202ms에 AC를 받는다. 비재귀 방식의 segTree도 있으며, 동일한 작업을 수행하지만, 

수행 방법은 조금 다른, Fenwick Tree도 존재한다. 수행시간은, 

Fenwick Tree =  140ms

<img width="876" alt="스크린샷 2024-07-26 오후 6 40 12" src="https://github.com/user-attachments/assets/342b44f3-0879-4638-8230-1502717f3950">


비재귀 Seg = 156ms

<img width="877" alt="스크린샷 2024-07-26 오후 6 32 32" src="https://github.com/user-attachments/assets/d7f41034-a74b-4f41-b926-24153ae89373">

재귀 Seg = 202ms

<img width="880" alt="스크린샷 2024-07-26 오후 6 32 58" src="https://github.com/user-attachments/assets/1ffb16cb-186f-4c4b-a12e-4ad7491fafb9">

이렇게 수행시간에 유의미한 차이가 나는 이유는, fenwick tree의 경우, bit 연산 기반이므로, 비재귀 방식의 세그먼트 트리보다 근소하게 빠르고, 

재귀 방식의 세그먼트 트리는, 재귀 호출 오버헤드 때문에, 비재귀 방식의 세그먼트 트리보다 더 느릴 수밖에 없다. 

주어지는 배열의 길이 n이 커짐에 따라서 수행 시간의 차이는 더욱 커진다. 이 문제에서는, $n \leq 10^5$ 이므로 비교적 n이 작지만, n이 커지면, Fenwick tree

로만 AC를 받을 수 있는 문제도 있다.

더욱이, 기본적인 세그먼트 트리는 점 업데이트, 구간 쿼리(PURQ) 만 지원하며, 만약 **구간** 업데이트, 구간 쿼리(RURQ)를 지원하는 세그먼트 트리를 만들기 위해서는, 

**Segment Tree with Lazy Propagation(느리게 갱신되는 세그먼트 트리)** 를 이용해야 하지만, Fenwick tree는 그냥 그 자체로 PURQ, RURQ를 모두 지원하므로,

더욱 편하다고 볼 수 있다. 

