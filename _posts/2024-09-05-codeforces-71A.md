---
title: "codeforces-71A"
excerpt: "코드포스 71A 문제 해결"

categories:
  - string
tags:
  - [tag1, tag2]

permalink: /string/codeforces-71A/

toc: true
toc_sticky: true

date: 2024-09-05
last_modified_at: 2024-09-05
---

## 🦥 본문

### 문제 링크  

[codeforces 71A](https://codeforces.com/problemset/problem/71/A)

### 풀이 시간 

아이디어 : -0:21t + 구현 : -1:45t = -2:06t

### 문제 설명 

간단한 문제이다. 그냥 입력받은 문자열의 길이가 10이하면 그 문자를 그대로 출력하고, 10 초과이면, **첫 철자 + 첫, 마지막 철자를 제외한 길이 + 마지막 철자** 의 형식으로 출력하면 된다.

```cpp
// codeforces 71A
// https://codeforces.com/problemset/problem/71/A
#include <bits/stdc++.h>
#define MAX 10
using namespace std;

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int tc; cin >> tc;
    while(tc--) {
        string str; cin >> str;
        int size = str.size();
        if(size <= MAX) {
            cout << str << "\n";
        } else {
            cout << str.front() << size - 2 << str.back() << "\n";
        }
    }
    return 0;
}
```

### 총평

<img width="1170" alt="스크린샷 2024-09-05 오전 1 50 50" src="https://github.com/user-attachments/assets/241f9f27-2839-480a-b03b-686cb1db3c3b">

61ms에 AC를 받는다. 쉬운 문제였다. 

