---
title: "ITMOacademy-binarysearch-1B,C"
excerpt: "코드포스 ITMO academy pilot course 이분탐색 step1 B, C번"

categories:
  - search
tags:
  - [tag1, tag2]

permalink: /search/ITMOacademy-binarysearch-1B, C/

toc: true
toc_sticky: true
use_math : true

date: 2024-07-25
last_modified_at: 2024-07-25
---

## 🦥 본문

### 아직 글 미완성입니다

### 문제 링크

[ITMOacademy-binarysearch-1B](https://codeforces.com/edu/course/2/lesson/6/1/practice/contest/283911/problem/B)

[ITMOacademy-binarysearch-1C](https://codeforces.com/edu/course/2/lesson/6/1/practice/contest/283911/problem/C)

### 풀이 시간

생략

### 문제 설명

정렬된 배열이 주어진다. 그리고 어떤 값 m이 주어지는데, 이때, 주어진 배열에서, 

B : m보다 작거나 같은 수 중 가장 큰 수 출력. 

C : m보다 크거나 같은 수 중 가장 작은 수 출력.

### 문제 풀이

**binary search** 문제이고, B, C는 동일한 작업을 반대로 하면 되는데, 일단 B번 문제에 대해 살펴보자. 

이 작업을 하기 위해서는, 기존의 이분 탐색 코드를 약간 수정하면 된다. 수정한 코드는 다음과 같다. 

```cpp
// 주어진 m보다 작거나 같은 원소 중 가장 큰 원소의 index를 반환, 1-based indexing 지원
int binary_search_modified(vector<int>& a, int m) {
    int l = -1, r = a.size();
    while(l + 1 < r) {
        int mid = (l + r) >> 1;
        if(a[mid] <= m) l = mid;
        else r = mid;
    }
    if(l != a.size() && l > -1) return l + 1;
	else return max(0, l + 1);
}
```

그리고, C번 문제에 대해서는, 

```cpp
// 주어진 m보다 크거나 같은 원소 중 가장 작은 원소의 index를 반환, 1-based indexing 지원
int binary_search_modified(vector<int>& a, int m) {
    int l = -1, r = a.size();
    while(l + 1 < r) {
        int mid = (l + r) >> 1;
        if(a[mid] < m) l = mid;
        else r = mid;
    }
    return r + 1;
}
```

이렇게 코드를 수정하면 된다. 

완성된 각각의 전체 코드는 다음과 같다. 

```cpp
// ITMO pilot course binary search #2
// https://codeforces.com/edu/course/2/lesson/6/1/practice/contest/283911/problem/B
#include <bits/stdc++.h>

using namespace std;

// 주어진 m보다 작거나 같은 원소 중 가장 큰 원소의 index를 반환, 1-based indexing 지원
int binary_search_modified(vector<int>& a, int m) {
    int l = -1, r = a.size();
    while(l + 1 < r) {
        int mid = (l + r) >> 1;
        if(a[mid] <= m) l = mid;
        else r = mid;
    }
    if(l != a.size() && l > -1) return l + 1;
	else return max(0, l + 1);
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int n, k; cin >> n >> k;
    vector<int> a(n, 0), query(k, 0);
    for(int i = 0; i < n; i++) cin >> a[i];
    for(int i = 0; i < k; i++) cin >> query[i];
    // 1-based indexing
    for(int i = 0; i < k; i++) cout << binary_search_modified(a, query[i]) << "\n";
    return 0;
}
```
```cpp
// ITMO pilot course binary search #3
// https://codeforces.com/edu/course/2/lesson/6/1/practice/contest/283911/problem/C
#include <bits/stdc++.h>

using namespace std;

// 주어진 m보다 크거나 같은 원소 중 가장 작은 원소의 index를 반환, 1-based indexing 지원
int binary_search_modified(vector<int>& a, int m) {
    int l = -1, r = a.size();
    while(l + 1 < r) {
        int mid = (l + r) >> 1;
        if(a[mid] < m) l = mid;
        else r = mid;
    }
    return r + 1;
}

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int n, k; cin >> n >> k;
    vector<int> a(n, 0), query(k, 0);
    for(int i = 0; i < n; i++) cin >> a[i];
    for(int i = 0; i < k; i++) cin >> query[i];
    // 1-based indexing
    for(int i = 0; i < k; i++) cout << binary_search_modified(a, query[i]) << "\n";
    return 0;
}
```

### 총평

이분 탐색의 응용 버전 문제였다. B번, C번 각각은, 108ms, 109ms에 AC를 받는다.

<img width="873" alt="스크린샷 2024-07-26 오후 3 59 04" src="https://github.com/user-attachments/assets/5b2f0f0b-34bd-4b78-968e-0ab880c82d1c">







