---
title: "Lazy propagation"
excerpt: "느리게 갱신되는 세그먼트 트리 설명"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/Lazy propagation/

toc: true
toc_sticky: true
use_math : true

date: 2024-08-03
last_modified_at: 2024-08-03
---

## 🦥 본문

### 기존 세그먼트 트리의 한계

세그먼트 트리는 분명 좋은 자료구조이다, 그러나, 한계가 명확하다. 만약 다음과 같은 쿼리가 주어진다면 어떻게 해야 할까? 

**구간 l ~ r까지, +v update** 

이런 쿼리가 주어지면, 기존 세그먼트 트리에서는, **구간 길이** 만큼 업데이트 쿼리를 진행해야 하고, 기존 세그먼트 트리의 업데이트 쿼리 시간 복잡도가 **O(log N)** 이라면, 

**O(N log N)** 으로 늘어난다. 이를 효율적으로 처리하기 위해 만들어진 자료구조가 lazy propagation이다. 

### Lazy propagation

앞서 말했듯, lazy propagation **이하 lazyseg** 는, 구간 업데이트 쿼리를 처리하기 위해서 만들어진 세그먼트 트리이다. 

세그먼트 트리와 동작 방식이 크게 다르지는 않지만, lazyseg는, **lazy 배열**을 이용해서 구간 업데이트를 처리하는 시간을 단축한다. 

#### lazy 배열의 사용

기존 세그먼트 트리의 동작 방식을 상기해보자. 업데이트  쿼리에서는, 루트 -> 리프로 내려가며, 해당 위치를 업데이트했다. 쿼리(여기서는 구간 합)도 마찬가지. 

lazy 배열은, lazyseg 라는 이름에서도 알 수 있듯이, **변화를 즉각적으로 반영하지 않고, 변화를 뒤로 미루는 역할** 을 수행한다. lazyseg 에서 업데이트나, 어떤 쿼리(구간 합 등)

를 수행할 때, 반드시 노드를 방문하게 되어 있다. 이때, 해당 노드에 해당하는 lazy배열에 값이 있는지를 확인한다. 만약 있다면, 

**현재 노드가 담당하는 구간 길이 * lazy배열 값** 만큼 변화를 주고, 자신의 lazy배열 값을, 자식 노드들에게 전파한다. 그리고, 자신은 update를 했으니, 자신의 lazy값은 다시 0으로 만든다. 

그럼, 예를 들어 보자. 구간 [3, 7] 에, 2를 더하고 싶다고 하자. 

그림으로 아주 깔끔하게 잘 나타낸 [boj Book](https://book.acmicpc.net/ds/segment-tree-lazy-propagation)의 자료를 빌려 쓰겠다. 

<img width="1009" alt="스크린샷 2024-08-03 오후 3 44 08" src="https://github.com/user-attachments/assets/16447e73-ed41-4736-8cad-8c3f17cd5004">

파란색 노드들은, 자신이 표현하는 구간과 업데이트하고자 하는 구간이 일부만 겹친다. 이 경우는, 기존의 세그먼트 트리와 동일하게 업데이트하면 된다. 

초록색 노드들은, 자신이 표현하는 구간이, 업데이트하고자 하는 구간에 완벽히 포함된다. 

이런 경우, **현재 노드의 두 자식 노드에 lazy 배열에 업데이트할 값을 넣어 두고, 나중에 업데이트를 한다.**

위의 그림에서는 다음과 같이 변한다. 노드 근처에 써 있는 숫자가 그 노드의 lazy값이다. 

<img width="991" alt="스크린샷 2024-08-03 오후 3 52 52" src="https://github.com/user-attachments/assets/84d02699-f11c-4f52-97de-e94037c99f4a">

주목할 부분은, 초록색 노드들의 값 변화인데, 지금 다루고 있는 세그먼트 트리에서의 어떤 노드는, 자신의 두 자식 노드의 합을 저장한다. 고로, 초록색 노드들은, 자신의 subtree의 모든 leaf node에, 2씩이 더해지는 것이다. 

그러면 결국 초록색 노드에는, 2 * (자신의 구간 길이) 만큼의 수가 더해져야만 하는 것이고, 이를 처리하고 자신의 자식 노드들에게 lazy값을 물려 주어야, **기존의 segtree의 한계처럼 여러 번 업데이트 하는 일이 없다.** 

#### update_lazy 함수의 구현

위의 내용을 바탕으로, **쿼리(업데이트, 구간 합 등) 를 수행할 때에는 먼저 lazy값이 있는지를 확인해야 한다는 것**을 알았다. 그럼, 이제 이를 위한 update_lazy 함수를 구현해 보자.

```cpp
    // 현재 방문하는 노드 node의 lazy 값이 있는지 확인하고, 있으면 실제로 업데이트 후 자식들에게 lazy값을 물려줌. 
    void update_lazy(IndexType node, IndexType start, IndexType end) {
        // lazy 배열의 값이 0이 아닌 경우, 업데이트를 해야 할 필요성이 있다.
        if (lazy[node] != 0) { 
            // 어차피 leaf node 까지 내려가면, node가 관장하는 부분의 값을 lazy 배열의 값만큼 일괄적으로 업데이트해야 하므로, 구간의 길이만큼 업데이트 한다. 
            tree[node] += (end - start + 1) * lazy[node];
            // leaf node가 아닌 경우, 자식 노드들에게 재귀적으로 lazy 값을 전파한다. 
            if (start != end) {
                lazy[node * 2] += lazy[node];
                lazy[node * 2 + 1] += lazy[node];
            }
            // 자식들에게 lazy값을 물려준 후, 현재 노드의 lazy값은 0으로 바꾼다.
            lazy[node] = 0;
        }
    }
```

이렇게 구현할 수 있다, 만약, 현재 노드에 lazy값이 있다면, 

1. 자신을 업데이트 : tree[node] += (end - start + 1) * lazy[node]
2. 자신의 두 자식 노드에게 lazy 값 전파
3. 자신의 lazy값은 0으로 세팅

함으로써 처리할 수 있다. 

### update_range, query 함수의 구현

다음과 같이 할 수 있다. 

```cpp
// 구간 일괄 업데이트
    void update_range(IndexType node, IndexType start, IndexType end, IndexType left, IndexType right, ValueType diff) {
        update_lazy(node, start, end);
        if (left > end || right < start) return;
        if (left <= start && end <= right) {
            tree[node] += (end - start + 1) * diff;
            if (start != end) {
                lazy[node * 2] += diff;
                lazy[node * 2 + 1] += diff;
            }
            return;
        }
        IndexType mid = (start + end) / 2;
        update_range(node * 2, start, mid, left, right, diff);
        update_range(node * 2 + 1, mid + 1, end, left, right, diff);
        tree[node] = tree[node * 2] + tree[node * 2 + 1];
    }

    // 구간 합 쿼리
    ValueType sum(IndexType node, IndexType start, IndexType end, IndexType left, IndexType right) {
        update_lazy(node, start, end);
        if (left > end || right < start) return 0;
        if (left <= start && end <= right) return tree[node];
        IndexType mid = (start + end) / 2;
        return sum(node * 2, start, mid, left, right) + sum(node * 2 + 1, mid + 1, end, left, right);
    }

    // codes ...

    ValueType query(IndexType left, IndexType right) {
        return sum(1, 0, size/2 - 1, left, right);
    }

    void update_range(IndexType left, IndexType right, ValueType diff) {
        update_range(1, 0, size/2 - 1, left, right, diff);
    }
```

눈여겨볼 점은, range_update, query 함수 호출 시에, 먼저 update_lazy 를 호출한다는 점이다. 이를 통해, 현재 방문하는 노드에 lazy값이 쌓여 있는지, 있다면 처리할 수 있다. 

이렇게 함으로써, 구간 업데이트지만, 기존의 세그먼트 트리와 동일한 시간 복잡도로, 작업을 처리할 수 있다. 

### 전체 코드

완성된 코드는 다음과 같다. 

```cpp
#include <bits/stdc++.h> 
using namespace std;
typedef long long ll;

template<typename ValueType, typename IndexType>
class Lazyprop {
private:
    vector<ValueType> tree;
    vector<ValueType> lazy;
    IndexType size;
    
    // 현재 방문하는 노드 node의 lazy 값이 있는지 확인하고, 있으면 실제로 업데이트 후 자식들에게 lazy값을 물려줌. 
    void update_lazy(IndexType node, IndexType start, IndexType end) {
        // lazy 배열의 값이 0이 아닌 경우, 업데이트를 해야 할 필요성이 있다.
        if (lazy[node] != 0) { 
            // 어차피 leaf node 까지 내려가면, node가 관장하는 부분의 값을 lazy 배열의 값만큼 일괄적으로 업데이트해야 하므로, 구간의 길이만큼 업데이트 한다. 
            tree[node] += (end - start + 1) * lazy[node];
            // leaf node가 아닌 경우, 자식 노드들에게 재귀적으로 lazy 값을 전파한다. 
            if (start != end) {
                lazy[node * 2] += lazy[node];
                lazy[node * 2 + 1] += lazy[node];
            }
            // 자식들에게 lazy값을 물려준 후, 현재 노드의 lazy값은 0으로 바꾼다.
            lazy[node] = 0;
        }
    }

    // 구간 일괄 업데이트
    void update_range(IndexType node, IndexType start, IndexType end, IndexType left, IndexType right, ValueType diff) {
        update_lazy(node, start, end);
        if (left > end || right < start) return;
        if (left <= start && end <= right) {
            tree[node] += (end - start + 1) * diff;
            if (start != end) {
                lazy[node * 2] += diff;
                lazy[node * 2 + 1] += diff;
            }
            return;
        }
        IndexType mid = (start + end) / 2;
        update_range(node * 2, start, mid, left, right, diff);
        update_range(node * 2 + 1, mid + 1, end, left, right, diff);
        tree[node] = tree[node * 2] + tree[node * 2 + 1];
    }

    // 구간 합 쿼리
    ValueType sum(IndexType node, IndexType start, IndexType end, IndexType left, IndexType right) {
        update_lazy(node, start, end);
        if (left > end || right < start) return 0;
        if (left <= start && end <= right) return tree[node];
        IndexType mid = (start + end) / 2;
        return sum(node * 2, start, mid, left, right) + sum(node * 2 + 1, mid + 1, end, left, right);
    }

public:
    Lazyprop(const vector<ValueType>& input) {
        ValueType n = input.size();
        IndexType h = (int)ceil(log2(n));
        size = 1 << (h + 1);
        tree.resize(size, 0);
        lazy.resize(size, 0);
    }

    ValueType query(IndexType left, IndexType right) {
        return sum(1, 0, size/2 - 1, left, right);
    }

    void update_range(IndexType left, IndexType right, ValueType diff) {
        update_range(1, 0, size/2 - 1, left, right, diff);
    }
};
int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    
    int n, m; cin >> n >> m;
    vector<ll> a(n, 0);
    Lazyprop<ll, int> lazyseg(a);

    while(m--) {
        int op; cin >> op;
        if(op == 1) {
            int l, r; ll v; cin >> l >> r >> v;
            lazyseg.update_range(l, r - 1, v);
        } else {
            int idx; cin >> idx;
            cout << lazyseg.query(idx, idx) << "\n";
        }
    }
    return 0;
}
```















