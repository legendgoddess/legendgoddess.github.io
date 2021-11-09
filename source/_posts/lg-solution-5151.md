---
title: HKE与他的小朋友
date: 2021-09-23 08:49:44
tags: 
  - 数论
categories: 
  - 洛谷题解
---

<h3><center>P5151 HKE与他的小朋友</center></h3>

> [P5151 HKE与他的小朋友](https://www.luogu.com.cn/problem/P5151)

说实在的这个对于置换的理解需要略微深一点。

首先可以直接将其看成一个置换：
$$
\left[
\begin{matrix}
1 & 2 & 3 & 4 & 5 & \dots & n \\
a_1 & a_2 & a_3 & a_4 & a_5 & \dots & a_n
\end{matrix}
\right]
$$
这个的具体含义就是编号为 $i$ 的位置之后会变成 $a_i$。那么显然对于一个置换会有若干个置换环，我们直接对于每一个置换环进行处理即可，复杂度是 $O(n)$ 的。

最后我们再将其反过来即可。

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

#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

int n, K;
vector<int> vc[maxn];
int siz[maxn], tot(0), a[maxn], vis[maxn];
int ans[maxn], f[maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, K);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= n; ++ i) if(!vis[i]) {
        ++ tot;
        for(j = i; !vis[j]; j = a[j]) {
            ++ siz[tot];
            vc[tot].push_back(j);
            vis[j] = 1;
        }
    }
    for(i = 1; i <= tot; ++ i) {
        int tmp = K % siz[i];
//        printf("%d %d\n", i, tmp);
//        for(int v : vc[i]) printf("%d : %d\n", i, v);
        for(j = 0; j < siz[i]; ++ j) {
            ans[vc[i][j]] = vc[i][(j + tmp) % siz[i]];
        }
    }
    for(i = 1; i <= n; ++ i) f[ans[i]] = i;
    for(i = 1; i <= n; ++ i) printf("%lld%c", f[i], " \n"[i == n]);
	return 0;
}

```




