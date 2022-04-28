---
title: "P7987 [USACO21DEC] Paired Up G 题解"
date: 2022-4-28 20:27:00
tags:
  - Dp
categories:
  - USACO题解
---

#### [P7987 [USACO21DEC] Paired Up G](https://www.luogu.com.cn/problem/P7987)

首先考虑我们匹配肯定是相邻匹配的，之后还有一种可能是间隔一个匹配的。

对于这样进行 $\tt Dp$ 设 $f(i, j)$ 其中 $j$ 是 $0/1$ 表示 $1 \sim i$ 未匹配点的奇偶性。

考虑如下 $4$ 种类转移，设 `z = i & 1`，$las$ 表示最大的 $x_i - x_{las} > K$ 的位置。

- 中间有一段是可以内部匹配的 $f(i, z) = f(las, !z) + y_i$。
- $x_i - x_{i - 1} \le K$ ，考虑让 $i, i - 1$ 匹配 $f(i, z) = f(i - 1, z)$。

- $x_{i + 1} - x_i \le K$，考虑让 $i, i + 1$ 匹配，那么匹配点的奇偶性改变 $f(i, !z) = f(i - 1, !z)$。
- $x_{i + 1} - x_i \le K$，考虑跳过这个位置，有 $f(i, !z) = f(las, z) + y_i$。

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

const int maxn = 2e5 + 5;
int n, m;
int x[maxn], y[maxn];
int f[maxn][2];

void Min(int& x,int c) { x = min(x, c); }

void Solve() {
    int i, j, T, op = 1;
    r1(T, n, m);
    if(T == 2) op = -1;
    for(i = 1; i <= n; ++ i) r1(x[i], y[i]), y[i] *= op;
    memset(f, 0x3f, sizeof(f));
    f[0][0] = 0;
    int ps(0);
    for(i = 1; i <= n; ++ i) {
        while(x[i] - x[ps + 1] > m) ++ ps;
        int z = (i & 1);
        Min(f[i][z], f[ps][!z] + y[i]);
        if(x[i] - x[i - 1] <= m) Min(f[i][z], f[i - 1][z]);
        if(x[i + 1] - x[i] <= m) Min(f[i][!z], f[i - 1][!z]);
        if(x[i + 1] - x[i - 1] <= m) Min(f[i][!z], f[ps][z] + y[i]);
    }
    int res = f[n][n & 1] * op;
    printf("%d\n", res);
}

signed main() {
	int i, j, T(1);
//    r1(T);
    while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

