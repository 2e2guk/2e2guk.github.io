---
title: "ITMOacademy-segTree-2A"
excerpt: "코드포스 ITMO academy pilot course segTree step2 A번"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/ITMOacademy-segTree-2A/

toc: true
toc_sticky: true
use_math : true

date: 2024-07-31
last_modified_at: 2024-07-31
---

## 🦥 본문

### 문제 링크

[ITMOacademy-segTree-2A](https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/A)

### 풀이 시간 

생략

### 문제 설명

다음 두 가지 쿼리를 처리하는 세그먼트 트리를 설계하라.

update(i, v) -> i번 index의 값을 v로 변경

query() -> 전 구간의, 배열의 부분 합 중 최대값을 구해 출력

### 문제 풀이

[금광 세그](https://2e2guk.github.io/range-query/금광%20세그/)를 이용하는 기본 문제이다. 다만 주의할 점은, 만약, 부분 배열의 최대 합을 구했는데 음수면, 그냥 아무것도 더하지 않는 것으로 생각해, 0을 리턴한다. 

코드는 다음과 같다.

```cpp
// ITMO pilot course segTree pt1, step2 #1
// https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/A
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
 
template <typename ValueType, typename IndexType>
struct GoldenSeg {
    vector<ValueType> arr, pref, suff, ans, sum;
    ValueType n;
 
    GoldenSeg(const vector<ValueType>& input) {
        n = input.size();
        arr = input;
        pref = sum = ans = suff = vector<ValueType>(n * 4);
        build(1, 0, n - 1);
    }
 
    void build(IndexType i, IndexType tl, IndexType tr) {
        if (tl == tr) {
            suff[i] = ans[i] = pref[i] = max((ValueType)0, arr[tl]);
            sum[i] = arr[tl];
        } else {
            IndexType tm = (tl + tr) / 2;
            build(i * 2, tl, tm);
            build(i * 2 + 1, tm + 1, tr);
            ans[i] = max(ans[i * 2], max(ans[i * 2 + 1], suff[i * 2] + pref[i * 2 + 1]));
            pref[i] = max(pref[i * 2], sum[i * 2] + pref[i * 2 + 1]);
            suff[i] = max(suff[i * 2 + 1], sum[i * 2 + 1] + suff[i * 2]);
            sum[i] = sum[i * 2] + sum[i * 2 + 1];
        }
    }
 
    void update(IndexType i, IndexType tl, IndexType tr, IndexType p) {
        if (tl == tr) {
            suff[i] = ans[i] = pref[i] = max((ValueType)0, arr[tl]);
            sum[i] = arr[tl];
        } else {
            IndexType tm = (tl + tr) / 2;
            if (tm >= p) update(i * 2, tl, tm, p);
            else update(i * 2 + 1, tm + 1, tr, p);
            
            ans[i] = max(ans[i * 2], max(ans[i * 2 + 1], suff[i * 2] + pref[i * 2 + 1]));
            pref[i] = max(pref[i * 2], sum[i * 2] + pref[i * 2 + 1]);
            suff[i] = max(suff[i * 2 + 1], sum[i * 2 + 1] + suff[i * 2]);
            sum[i] = sum[i * 2] + sum[i * 2 + 1];
        }
    }
 
    void update(IndexType idx, ValueType val) {
        arr[idx] = val;
        update(1, 0, n - 1, idx);
    }
    struct Result {
        ValueType ans, pref, suff, sum;
    };
 
    Result merge(const Result &left, const Result &right) {
        Result res;
        res.ans = max({left.ans, right.ans, left.suff + right.pref});
        res.pref = max(left.pref, left.sum + right.pref);
        res.suff = max(right.suff, right.sum + left.suff);
        res.sum = left.sum + right.sum;
        return res;
    }
 
    Result query(IndexType i, IndexType tl, IndexType tr, IndexType l, IndexType r) {
        if (l > r) {
            return {numeric_limits<IndexType>::min(), numeric_limits<IndexType>::min(), numeric_limits<IndexType>::min(), 0};
        }
        if (tl == l && tr == r) {
            return {ans[i], pref[i], suff[i], sum[i]};
        }
        IndexType tm = (tl + tr) / 2;
        return merge(query(i * 2, tl, tm, l, min(r, tm)),
                     query(i * 2 + 1, tm + 1, tr, max(l, tm + 1), r));
    }
    Result query(IndexType l, IndexType r) {
        return query(1, 0, n - 1, l, r);
    }
};
 
int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    ll n, m; cin >> n >> m;
    vector<ll> input(n);
    for (auto &x : input) cin >> x;
 
    GoldenSeg<ll, int> segTree(input);
    cout << segTree.query(0, n - 1).ans << "\n";
 
    while (m--) {
        ll x, y; cin >> x >> y;
        segTree.update(x, y);
        cout << segTree.query(0, n - 1).ans << "\n";
    }
    return 0;
}
```

ans, suff, pref 초기화 과정에서, 배열의 값을 그대로 넣는 것이 아닌, 만약 음수인 경우, 0을 넣는 것을 살펴볼 수 있다. 

이렇게 함으로써, 만약 부분 배열의 합의 최대가 음수인 경우, 문제의 의도대로 0을 출력하게 한다. 

### 총평

[금광 세그](https://2e2guk.github.io/range-query/금광%20세그/) 의 가장 기본적인 문제들 중의 하나이다, 금광 세그를 이해하는데 시간이 좀 걸렸어서, 

잘 알아두어야 할 테크닉인 것 같다. 

정답 코드는 218ms에 AC를 받는다. 

<img width="876" alt="스크린샷 2024-07-31 오후 8 36 18" src="https://github.com/user-attachments/assets/8e34decc-3df1-46e1-b63f-14c6a7616cb7">








