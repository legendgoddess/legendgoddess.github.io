---
title: "CF1453F Even Harder 题解"
date: 2022-3-31 8:52:00
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1453F - Codeforces](https://codeforces.com/problemset/problem/1453/F)

感觉按照图是不好做的，我们考虑这个序列是有特殊性质的，所以我们考虑按照序列做。

最终序列是固定的，考虑对于最终序列进行 $\tt Dp$，我们会需要记录最终序列，我们发现如果加入一个点的时候我们只需要知道当前序列的最近两个位置即可，不妨假设其为 $(u, v)$。

对于加入的点 $x$，我们肯定有 $u \le a_x + x < v$。

其次对于 $y \in [x + 1, u - 1]$，我们需要保证这些点是不能到达 $u$ 的所以需要删除贡献。

那么设 $f(u, v)$ 表示最后两个元素为 $u, v$，考虑转移到新的点 $v'$ 方程：

$$
f(u, v) = \min_{k = 1} ^ {u - 1} (f(k, u) + \sum_{p = k + 1} ^ {u - 1} [a_p + p\ge u])
$$

这个东西是 $O(n^3)$ 的。

我们考虑枚举 $u$ 之后，需要考虑剩下的条件也就是 $u \le a_k + k < v$，我们可以离线存下来，之后开桶按照 $a_k + k$ 开桶，然后更新每个 $v$。

我们不妨将状态稍微修改一下，我们发现后面的部分就是可以包含在 $f(k, u)$ 内的，所以我们可以得到新的 $f(u, v)$：

$$
f(u, v) = \min_{k = 1} ^ {u - 1} f(k, u) + \sum_{p = u + 1} ^ {v - 1} [a_p + p \ge v]
$$

考虑对于左边的部分进行差分前缀 $\min$，后面的部分对于同一个 $u$ 我们可以直接顺序枚举 $v$，后面的部分还是可以通过差分解决。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
//		#define Fread
		#ifdef Fread
		const int Siz = (1 << 21) + 5;
		char *iS, *iT, buf[Siz];
		#define gc() ( iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iS == iT ? EOF : *iS ++) : *iS ++ )
		#define getchar gc
		#endif
		template <typename T>
		void r1(T &x) {
		    x = 0;
			char c(getchar());
			int f(1);
			for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
			for(; isdigit(c); c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
			x *= f;
		}
		template <typename T, typename...Args>
		void r1(T &x, Args&...arg) {
			r1(x), r1(arg...);
		}
		#undef getchar
	}

using namespace Read;

const int maxn = 3e3 + 5;
int n, m, a[maxn], f[maxn][maxn];
int sum[maxn], g[maxn];

void Min(int& c,int x) { c = min(c, x); }

void Solve() {
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 0; i <= n; ++ i) for(j = 0; j <= n; ++ j) f[i][j] = 0x3f3f3f3f;
    f[0][1] = 0;
    for(int u = 1; u < n; ++ u) {
        for(i = 0; i <= n; ++ i) sum[i] = 0x3f3f3f3f, g[i] = 0;
        for(i = 0; i < u; ++ i) Min(sum[a[i] + i], f[i][u]);
        for(i = 0; i < n; ++ i) Min(sum[i + 1], sum[i]);
        for(i = u + 1; i <= n; ++ i) {
            int tmp = a[i] + i;
            if(tmp < i + 1) continue;
            g[i] ++, g[tmp] --;
        }// g[i] : x in [1, i] and x >= i + 1
        for(int v = u + 1; v <= n; ++ v) if(a[u] + u >= v) {
            g[v] += g[v - 1];
            Min(f[u][v], sum[v - 1] + g[v - 1]);
        }
    }
    int ans(0x3f3f3f3f);
//    for(i = 1; i < n; ++ i) {
//        for(j = i + 1; j <= n; ++ j) printf("f[%d][%d] = %d\n", i, j, f[i][j]);
//    }
    for(i = 1; i < n; ++ i) ans = min(ans, f[i][n]);
    printf("%d\n", ans);
}

signed main() {
	int i, j, T;
    r1(T); while(T --) Solve();
	return 0;
}
/*
1
5
4 3 2 1 0
*/
}


signed main() { return Legendgod::main(), 0; }//


```
