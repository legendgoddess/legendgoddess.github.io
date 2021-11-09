---
title: CF1043F Make It One
date: 2021-09-14 19:47:00
tags:
  - 数论
  - 反演
categories:
  - CF题解
---

<h3><center>CF1043F Make It One</center></h3>

>  [CF1043F Make It One](https://www.luogu.com.cn/problem/CF1043F)

---

首先看一下质因子个数最多只有 $6$ 个，考虑答案上界是多少。

```cpp
2 3 5 7 11 13
3 5 7 11 13 17
```

> **不要忘记** $17$。

你们答案的上界就是 $7$。

我们考虑对于 $\gcd$ 进行卷积，那么我们需要使用容斥。

考虑反演：
$$
g = f \times I \\
f = g \times \mu
$$

> 原理就是 $I \times \mu = e$。

但是反演其实还有一种形式。
$$
g(n) = \sum_{i = 1, i \times n \le m} f(i \times n) \\
f(n) = \sum_{i = 1, i \times n \le m} g(i \times m) \times \mu(i)
$$
我们直接使用后面一种即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
//#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
#define getchar gc
#endif // Fread

template <typename T>
void r1(T &x) {
	x = 0;
	char c(getchar());
	int f(1);
	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
	x *= f;
}

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 3e5 + 5;
const int maxm = maxn << 1;

int tot(0);
int pr[maxn / 10], vis[maxn], mu[maxn];
int n, m;

void init(int x) {
    for(int i = 2; i <= x; ++ i) {
        if(!vis[i]) pr[++ tot] = i, mu[i] = -1;
        for(int j = 1, k; k = i * pr[j], j <= tot && k <= x; ++ j) {
            vis[k] = 1;
            if(!(i % pr[j])) {
                mu[k] = 0; break;
            }
            mu[k] = - mu[i];
        }
    }
}

vector<int> operator * (const vector<int>& a, const vector<int>& b) {
    vector<int> ans(m + 2, 0);
    int i, j;
    for(i = 1; i <= m; ++ i) {
        int t1(0), t2(0);
        for(j = i; j <= m; j += i)
            t1 += a[j], t2 += b[j];
        ans[i] = t1 * t2;
    }
    for(i = 1; i <= m; ++ i) {
        for(j = 2; j * i <= m; ++ j)
            ans[i] += mu[j] * ans[i * j];
    }
    return ans;
}
signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    init(3e5);
    r1(n);
    vector<int> c(n + 1, 0);
    for(i = 1; i <= n; ++ i) {
        r1(c[i]);
        m = max(m, c[i]);
    }

    vector<int> a(m + 2, 0), b(m + 2, 0);
    for(i = 1; i <= n; ++ i) ++ a[c[i]], ++ b[c[i]];

    for(i = 1; i <= 7; ++ i) {
        if(b[1]) return printf("%d\n", i), 0;
        b = b * a;
    }
	return 0;
}

```




