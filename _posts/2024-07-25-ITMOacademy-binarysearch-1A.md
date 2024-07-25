---
title: "ITMOacademy-binarysearch-1A"
excerpt: "코드포스 ITMO academy pilot course 이분탐색 step1 A번"

categories:
  - search
tags:
  - [tag1, tag2]

permalink: /search/ITMOacademy-binarysearch-1A/

toc: true
toc_sticky: true
use_math : true

date: 2024-07-25
last_modified_at: 2024-07-25
---

## 🦥 본문

### 문제 링크

[codeforces-ITMOacademy-binarysearch-1A](https://codeforces.com/edu/course/2/lesson/6/1/practice/contest/283911/problem/A)

### 풀이 시간

아이디어 : -0:34t + 구현 -2:45t = - 4:09t

### 문제 설명

정렬된 배열이 주어지고, k개의 쿼리 값이 주어진다. k개의 쿼리 값에 대해, 주어진 값이 배열 안에 있다면 YES, 없다면 NO를 출력하라.

### 문제 풀이

**search, binary search** 문제이다. 완성된 코드는 다음과 같다. 

```cpp
// ITMO pilot course binary search #1
// https://codeforces.com/edu/course/2/lesson/6/1/practice/contest/283911/problem/A
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
    int n, k; cin >> n >> k;
    vector<int> a(n, 0), query(k, 0);
    for(int i = 0; i < n; i++) cin >> a[i];
    for(int i = 0; i < k; i++) cin >> query[i];
    for(int i = 0; i < k; i++) {
        if(binary_search(a, query[i]) != -1) cout << "YES" << "\n";
        else cout << "NO" << "\n";
    }
    return 0;
}
```

### 총평

가장 기본적인 이분탐색 문제이다. 정답 코드는 109ms에 AC를 받는다.

제출 정보 : <img width="872" alt="스크린샷 2024-07-25 오전 9 59 41" src="https://github.com/user-attachments/assets/1255d610-4507-450c-963e-b28b7bc26bcd">





