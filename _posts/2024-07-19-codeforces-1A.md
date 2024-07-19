---
title: "codeforces-1A"
excerpt: "코드포스 1A문제 해결"

categories:
  -math
tags:
  - [tag1, tag2]

permalink: /math/codeforces-1A/

toc: true
toc_sticky: true

date: 2024-07-19
last_modified_at: 2024-07-19
---

## 🦥 본문
문제 링크 : [codeforces 1A](https://codeforces.com/problemset/problem/1/A)

풀이시간 :  아이디어 : -2:18t + 구현 : -9:32t = -11:50t

문제 설명 :  간단한 문제이다. n * m 크기의 직사각형을, a * a 크기의 정사각형(이하 flagstone)으로 모두 덮기 위한 최소 flagstone 의 개수를 구하는 문제이다. 

간단히 풀었다. 일단, n / a 를 하면, 가로(n)를 덮기 위한 최소 개수의 flagstone 의 수가 나온다. 같은 방식으로 m / a 를 하면 세로(m)를 덮기 위한 최소 개수의 flagstone 이 나온다. 
이 둘을 곱하기 전에, 나머지가 남는 경우를 생각할 수 있다. 예를 들면,

문제의 tc인 6 6 4 의 경우, 가로 최소 1개, 그리고 2만큼의 길이가 남고, 세로도 마찬가지다. 이렇게 길이가 남을 때는, 어쩔 수 없이 타일 1개를 더 깔아야 한다. 

그럼 이를, 다음과 같이 구현할 수 있다. 

```cpp
ll rowmin = n / a;
ll colmin = m / a;
if((n - rowmin * a) % a != 0) rowmin++;
if((m - colmin * a) % a != 0) colmin++;
```
이렇게 구현이 가능하다. 만약 나머지가 남는다면, 무조건 하나를 더해주어야 하는 것이다. 

전체 코드는 다음과 같다. 

```cpp
// codeforces 1A
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

int main() {
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    ll n, m, a; cin >> n >> m >> a;
    ll rowmin = n / a;
    ll colmin = m / a;
    if((n - rowmin * a) % a != 0) rowmin++;
    if((m - colmin * a) % a != 0) colmin++;
    ll ans  = rowmin * colmin;
    cout << ans;
    return 0;
}
```

총평은, 그냥 쉬운 문제였다. 
