---
title: CF1197E Culture Code
date: 2021-10-08 10:34:11
tags:
  - 图论
  - Dp
categories:
  - CF题解
---


<h3><center>CF1197E Culture Code</center></h3>

> [CF1197E Culture Code](https://codeforces.ml/problemset/problem/1197/E)

显然 $\tt Dp$ 肯定是不能依赖于体积的。我们考虑选择当前位置的方案。

容易发现每个点能选择的对象构成了一个 $\tt DAG$。

设 $f(i)$ 表示选择了点 $i$ 的最小剩余体积，显然 $f(i) = -in(i) + \min_{j} f(j) + out(j)$，后面的东西直接使用前缀维护即可。

对于方案数我们可以同样维护一个前缀和，但是其代表的是考虑了点 $i$ 得到最终答案是 $f(i)$ 的最小方案。

设 $g(i)$ 表示最终最小值是 $f(i)$ 的时候，考虑了 $1 \sim i$ 的答案。

> 显然这个东西包含了之前的所有合法方案。
>
> 因为如果当前点是终止点，之后不会让其贡献重复叠加。
>
> 如果当前点不是终止点，那么其贡献还可能更新到之后的点改变。

但是我们需要保证 $out$ 的合法性，直接按照 $out$ 排序，之后二分得到即可。

------

本题比较难处理的就是 $\tt Dp$ 的边界，但是知道这个是拓扑图了之后肯定就没有问题了。

- 起始点，显然其 $in < \min_j out(j)$。
- 终止点，也就是不可能有别的点包含它 $out > \max_j in(j)$。

------

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
const int maxn = 2e5 + 5;
const int mod = 1e9 + 7;
const int maxm = maxn << 1;

struct Node {
    int in, ot;
}p[maxn];

int n, ot[maxn], f[maxn], g[maxn];
int mn[maxn], sum[maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(p[i].ot, p[i].in), ot[i] = p[i].ot;
    sort(ot + 1, ot + n + 1);
    sort(p + 1, p + n + 1, [&](const Node &a, const Node &b) {
        return a.ot == b.ot ? a.in < b.in : a.ot < b.ot;
     });

    int mnf(2e9), mo(2e9), mxi(0);
    for(i = 1; i <= n; ++ i) mo = min(mo, p[i].ot), mxi = max(mxi, p[i].in);

    for(i = 1; i <= n; ++ i) if(p[i].in < mo) g[i] = 1;
    for(i = 1; i <= n; ++ i) {

        int x = upper_bound(ot + 1, ot + n + 1, p[i].in) - ot - 1;
//        printf("%d : %d\n", i, x);
        f[i] = mn[x] + p[i].in;
        if(!g[i]) g[i] = sum[x];

        int tmp = f[i] - p[i].ot;

        if(tmp == mn[i - 1] && i != 1) {
            sum[i] = (sum[i - 1] + g[i]) % mod;
        }
        else if(tmp < mn[i - 1] || i == 1) {
            sum[i] = g[i];
        }
        else sum[i] = sum[i - 1];

        mn[i] = min(mn[i - 1], tmp);
        if(p[i].ot > mxi) mnf = min(mnf, f[i]);
    }

//    printf("mnf = %d\n", mnf);

//    for(i = 1; i <= n; ++ i) printf("%d : %d %d\n", i, f[i], g[i]);

    int ans(0);
    for(i = 1; i <= n; ++ i) if(p[i].ot > mxi && f[i] == mnf) ans = (ans + g[i]) % mod;
    printf("%d\n", ans);

	return 0;
}
```



