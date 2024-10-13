---
title: "Sqrt_decomposition"
excerpt: "제곱근 분할법 설명"

categories:
  - range-query
tags:
  - [tag1, tag2]

permalink: /range-query/Sqrt_decomposition/

toc: true
toc_sticky: true
use_math : true

date: 2024-10-14
last_modified_at: 2024-10-14
---

## 🦥 본문

이 글의 사진의 [출처](https://justicehui.github.io/medium-algorithm/2019/03/03/SqrtDecomposition/) 는 출처 를 클릭하면 이동 가능합니다.

### 익숙한 쿼리

구간 쿼리의 대표적인 쿼리인, 다음과 같은 쿼리가 주어진다고 해 보자. 

1. update(u, i) -> u번 값을, i로 업데이트

2. query(l, r) -> [l, r] 구간 합 구하기

물론, 우리는 이런 쿼리가 주어진다면, 세그먼트 트리를 써야겠다고 생각할 수 있지만, 조금 다른, 어쩌면 더 직관적인 방식을 살펴보자. 

### sqrt decomposition

sqrt decomposition(제곱근 분할법)은, 주어진 N개의 값을, $\sqrt{N}$ 개의 그룹으로 나눈다. 

길이가 16인 배열이면 4개, 25인 배열이면 5개로 나누는 것이다. 그리고, 이렇게 나누어진 각 그룹을, **bucket** 이라고 한다. 

이 각 bucket 에는, 대푯값이 있는데, 이는 주어지는 쿼리가 어떤 종류인지에 따라서 달라진다. 만약 구간 합 쿼리가 주어진다면 구간 합일 것이고, 

구간 최소/최댓값 쿼리가 주어지면 구간 최소/최댓값을 대푯값으로 가질 것이다. 

이 대폿값이, "세그먼트 트리에서의 두 자식 노드의 부모 노드에 저장되는 값"이라고 생각하면 된다. 

예시를 들어보면, 구간 합을 대푯값으로, sqrt decompostion을 진행하면 다음과 같이 된다. 

<img width="701" alt="스크린샷 2024-10-14 오전 3 58 46" src="https://github.com/user-attachments/assets/79ba602d-e3c4-48ca-b466-32289948b86a">

시간 복잡도는 $O(\sqrt{N})$ 이다. 세그먼트 트리보다 느린데 왜 쓸까? 

세그먼트 트리보다 **공간 복잡도가 더 좋기 때문에**, 메모리 제한이 빡빡한 문제에서 사용한다. 그리고, 후에 설명할 Mo's의 기반이 되는 알고리즘이다. 

### update 함수의 구현

update 함수는 어떻게 구현할 수 있을까? sqrt decomposition의 아이디어가 어렵지 않고, 세그먼트 트리와 비슷한 방식으로 작동하므로, 어렵지 않게 구현을 어떻게 할지 떠올릴 수 있다. 

해당 위치의 원소의 값을 갱신하고, **구간의 대폿값을 갱신하면 된다**

그럼, 자연스럽게 드는 의문은, **지금 index가 어떤 bucket에 속하는지 어떻게 알 거냐..** 라는 것일 텐데, 이는 그냥 간단하게, 갱신하고자 하는 위치를 pos(0-based index)라고 하면,

$pos / \sqrt{N}$ 을, bucket의 id로 사용하면 된다. 

예를 들어 보자. 길이 9짜리 배열이 주어진다면, bucket의 개수는 3개고, 각 bucket에 속하는 배열 원소의 index만 써 보면 다음과 같다.

bucket **0** = **0, 1, 2** -> 0 / 3 = 0, 1 / 3 = 0, 2 / 3 = 0

bucket **1** = **3, 4, 5** -> 3 / 3 = 1, 4 / 3 = 1, 5 / 3 = 1

bucket **2** = **6, 7, 8** -> 6 / 3 = 2, 7 / 3 = 2, 8 / 3 = 2


이렇게, $pos / \sqrt{N}$ 을, bucket의 id로 사용하면, index만 주어지면, 잘 업데이트 할 수 있다. 

코드로는 다음과 같이 구현할 수 있다. 

```cpp
void update(ll idx, ValueType val) {
        ll id = idx / sq; // 그룹 번호
        auto &bucket = buckets[id];
        auto it = lower_bound(bucket.begin(), bucket.end(), arr[idx]);
        bucket.erase(it);
        bucket.insert(upper_bound(bucket.begin(), bucket.end(), val), val);
        arr[idx] = val;
    }
```

### query 처리

쿼리를 처리할 때에는, 하나만 생각하면 된다. **쿼리를 날리는 구간의 부분집합이, 어떤 bucket을 완벽히 포함하는가?** 무슨 말인지 감이 오지 않을 거라고 생각한다. 다음을 살펴보자. 

<img width="700" alt="스크린샷 2024-10-14 오전 4 22 04" src="https://github.com/user-attachments/assets/9142dce0-7164-400b-9d86-9c6b1cfc71ac">

이 경우는 4개의 bucket이 있고, 쿼리를 날리는 구간이 왼쪽 기준으로 첫 번째, 네 번째 bucket에, **완벽하게 포함되지 않**는다. (그림에서의 5, {2, 7, 3})

만약, 쿼리를 날리는 구간이, 어떤 bucket에 완벽하게 포함된다면, 그냥 단순히 그 구간을, bucket의 대푯값으로 대체할 수 있을 것이다. 그러나, 완벽히 포함하지 않는 경우는, 

**일일이 손수 처리** 해 주어야 하는 것이다. 

그럼, [l, r] 구간에 쿼리를 날릴 때, 어떻게 쿼리를 날리는 구간의 부분집합이, 어떤 bucket을 완벽히 포함하는지 알 수 있을까? 

관찰을 해 보면, 꽤 유용한 사실을 알 수 있다. 어떤 bucket의 시작 위치의 index를, **bucket_start** 라고 하면, 다음이 성립한다. 

$bucket_start % \sqrt{N} == 0$

고로, 우리는 l, r을 동시에 움직여 가면서, 

만약 $bucket_start % \sqrt{N} != 0$ 이면 개별 처리(l++, r--)하고, 

$bucket_start % \sqrt{N} == 0$이면, 한꺼번에 처리(l += \sqrt{N})하면 된다. 

코드로 구현하면, 다음과 같다. 참고할 것은, 이 코드의 쿼리는 구간 합이 **아니다**

```cpp
ValueType query(ValueType l, ValueType r, ValueType k) {
        ValueType ret = 0;
	    // index 개별 처리
        while(l % sq != 0 && l <= r) {
            if(arr[l] > k) ret++;
            l++;
        }
        while((r + 1) % sq != 0 && l <= r) {
            if(arr[r] > k) ret++;
            r--;
        }
	    // 하나의 bucket으로 한꺼번에 처리
        while(l <= r){
            ret += buckets[l / sq].end() - upper_bound(buckets[l / sq].begin(), buckets[l / sq].end(), k);
            l += sq;
        }
        return ret;
    }
```

### 연습 문제

(boj14504)[https://www.acmicpc.net/problem/14504] 

이 문제를 보자. 익숙한 쿼리들이 주어진다.. 그런데 메모리 제한이 512MB이다. 좀 작은데? 

이럴때 쓰는 게 제곱근 분할법이다. 

코드는 다음과 같다. 

```cpp
// 평방 분할(제곱근 분할법, sqrt decomposition)
// 주어진 쿼리를, O(sqrt(N))개의 그룹으로 분할해 결과를 구한다. -> 시간복잡도  O(sqrt(N))
// 0-based, main 함수 예시는 boj 14504
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

template<typename ValueType>
struct SqrtDecomposition {
    vector<ValueType> arr;
    vector<vector<ValueType>> buckets;
    ll sq;

	SqrtDecomposition(ll n, const vector<ValueType>& input) : arr(input) {
        sq = sqrt(n);
        buckets.resize((n + sq - 1) / sq); // 올림 처리를 통한 크기 설정
        for(ll i = 0; i < n; i++){
            buckets[i / sq].push_back(arr[i]);
        }
		// 이 부분은, 문제에 따라서 추가한 부분이다.
        for(ll i = 0; i < buckets.size(); i++) { // buckets.size()를 사용
            sort(buckets[i].begin(), buckets[i].end());
        }
    }

    void update(ll idx, ValueType val) {
        ll id = idx / sq; // 그룹 번호
        auto &bucket = buckets[id];
        auto it = lower_bound(bucket.begin(), bucket.end(), arr[idx]);
        bucket.erase(it);
        bucket.insert(upper_bound(bucket.begin(), bucket.end(), val), val);
        arr[idx] = val;
    }

    ValueType query(ValueType l, ValueType r, ValueType k) {
        ValueType ret = 0;
	    // index 개별 처리
        while(l % sq != 0 && l <= r) {
            if(arr[l] > k) ret++;
            l++;
        }
        while((r + 1) % sq != 0 && l <= r) {
            if(arr[r] > k) ret++;
            r--;
        }
	    // 하나의 bucket으로 한꺼번에 처리
        while(l <= r){
            ret += buckets[l / sq].end() - upper_bound(buckets[l / sq].begin(), buckets[l / sq].end(), k);
            l += sq;
        }
        return ret;
    }
};

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    ll n, m; cin >> n;
    vector<ll> arr(n);
    for(int i = 0; i < n; i++) cin >> arr[i];

    SqrtDecomposition<ll> root(n, arr);

    cin >> m;
    for(int i = 0; i < m; i++){
        int a; cin >> a;
        if(a == 2) {
            ll b, c; cin >> b >> c; b--;
            root.update(b, c);
        } else {
            ll l, r, val; cin >> l >> r >> val; l--; r--;
            cout << root.query(l, r, val) << "\n";
        }
    }
    return 0;
}
```

정답 코드는, 1024ms에 AC를 받는다. 

<img width="1142" alt="스크린샷 2024-10-14 오전 4 37 25" src="https://github.com/user-attachments/assets/5007eb95-ff23-4476-925d-84f39b5b4fe8">















