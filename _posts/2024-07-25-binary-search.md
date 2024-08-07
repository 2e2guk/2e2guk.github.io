---
title: "binary-search"
excerpt: "이분탐색 알고리즘"

categories:
  - search
tags:
  - [tag1, tag2]

permalink: /search/binary-search/

toc: true
toc_sticky: true
use_math : true

date: 2024-07-25
last_modified_at: 2024-07-25
---

## 🦥 본문

### 알고리즘 소개

어린 시절, 책에서든, 친구에게서든, "지금 내가 생각하는 수를 맞춰 봐" 하는 게임을 했던 기억이 있다. 보통, 1 ~ 10 사이에 있는 수 중에서 내가 생각하는 수를 맞춰 봐!

하는 형식이었는데, 이분 탐색은 이와 관련이 있는 알고리즘이다. 

### 알고리즘 설명

**binary search** 알고리즘은, 다음과 같은 문제를 해결한다. 

어떤 배열 $a[i]$ 가 있고, 이 배열은 정렬되어 있다. 이때, 우리는 어떤 값 v를 이 배열 a에서 찾고자 한다. 

#### naive 한 접근

naive 하게 접근한다면, 배열의 길이가 n이라 할 때, $O(n)$ 시간에, 배열의 처음부터 끝까지 하나하나 탐색하는 방법이 있다. 그런데, 이는 배열의 크기가 커짐에 따라서 비효울적으로 변한다. 

#### optimize

우리는, 이 배열이 **정렬** 되어 있다는 사실을 이용해, $O(\log_{2} n)$ 시간에 탐색을 완료할 수 있다. 

만약 정렬되어 있지 않다면, std::sort 함수가 $O(n \log n)$ 시간이 걸리므로, $O(n \log n)$ 시간에 탐색을 완료할 수 있다. 

배열의 가장 왼쪽 index를 l, 가장 오른쪽 index를 r 이라 하자. 그리고, 찾고자 하는 값이 m일 때, 다음을 반복한다. 

1. $mid = (l + r) / 2$
2. $a[mid] > m$ 이면 r = mid - 1, l은 그대로, $a[mid] < m$ 이면 l = mid + l, r은 그대로, $a[mid] = m$ 이면, 원하는 값을 찾은 것.

이 2개의 과정을, 원소가 하나 남을 때까지 반복한다. 만약, 하나 남은 원소마저 찾고자 하는 값 m이 아닌 경우, 검색에 실패한 것이며, 만약 찾고자 하는 값을 찾았으면 

그대로 탐색을 종료하면 된다. 

설명을 덧붙이자면, 이런 거랑 똑같다. 예를 들어서, 1 ~ 100 사이에 내가 생각하는 수를 맞춰 봐! 라는 말을 들으면, 일단 50을 부른다. 

만약, 내가 생각하는 수가 그것보다 작다면, 51 ~100 까지의 탐색은 의미 없으니까, 25를 부른다. 만약, 이것보다도 작다고 하면, 26 ~ 50 은 또 탐색할 필요가 없다. 

그럼 이제 12를 부르고, 12보다 크다고 하면, 1 ~ 12는 탐색할 필요가 없고... 이렇게 반복하는 것이다. 

#### 시간 복잡도 설명

주어지는 길이 n짜리 배열이 정렬된 경우, 이분 탐색은 $O(\log n)$ 의 시간이 걸리는데, 이 이유는, 

알고리즘의 과정이 한번 진행될 때마다, 탐색 범위가 현재 범위의 절반으로 줄어들기 때문이다.


### 코드

```cpp
// binary search
// 찾고자 하는 값이 있으면 그 값을 출력하고, 없으면 -1을 출력한다.  
#include <bits/stdc++.h>

using namespace std;

// 이분 탐색, return value는 찾고자 하는 값의 index.
int binary_search(vector<int>& a, int m) {
    int l = 0, r = a.size() - 1;
    while(l <= r) {
        int mid = (l + r) >> 1;
        if(a[mid] == m) return mid;
        else if(a[mid] < m) l = mid + 1;
        else if(a[mid] > m) r = mid - 1;
    }
    // 검색 실패에 해당하는 경우.
    return -1;
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    // n = 입력배열의 크기, m = 찾고자하는 값.
    int n, m; cin >> n >> m;
    vector<int> a(n, 0);
    for(int i = 0; i < n; i++) cin >> a[i];
    // 이분탐색은 정렬된 환경에서만 가능
    sort(a.begin(), a.end());
    int searchres = binary_search(a, m);
    if(searchres != -1) cout << a[searchres];
    else cout << searchres;
    return 0;
}
```




