---
title: "CF1474F 1 2 3 4 ... 题解"
date: 2022-4-3 13:49:00
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1474F - Codeforces](https://codeforces.com/problemset/problem/1474/F)

> 经典题。

首先最长严格上升子序列可以直接求得。

之后将其分成若干上升和下降的段，显然这样的段的**值域**肯定是连续的，或者说我们的最长严格上升子序列**值域**是连续的。

考虑对于这个东西进行 $\tt Dp$，发现我们只需要知道答案的最小值和最大值就可以确定一个数列。如果有多种这样的答案，对于任意两个答案序列肯定是不交的。

> 如果相交就可以获得更长的答案。

所以对于这个东西可以分开计算，具体来说就是枚举最小值，之后考虑将序列分成上升和下降的段。

发现按照段进行转移是不方便的， 所以考虑按照值域进行转移。

我们考虑枚举之后设 $f(i, j)$ 表示当前值为 $i$，处于第 $j$ 段的方案数。

这个东西可以矩阵加速，复杂度是 $O(n \times n^3 \log V)$。

发现一个问题，如果直接只考虑左右端点的话会发现部分转移没法转移到。

所以需要拆点拆成一个可以使其转移到的位置，也就是拆成 $3$ 份。

我们具体来说说：

```cpp
    for(i = 1; i <= totd; ++ i) {
        if(d[i] <= last || d[i] > 1 + ans) continue;
        B.clear();
        for(j = 1; j <= tot; ++ j) if(min(l[j], r[j]) <= last + 1 && max(l[j], r[j]) >= d[i]) {
            for(int k = 0; k <= j; ++ k)
                B[k][j] = 1;
            if(l[j] >= r[j]) B[j][j] = 0;
        }
        ksm(d[i] - last), last = d[i];
    }
```

这个是之前的转移，其中 $d_i$ 是所有左右端点排序去重之后的结果。

对于第 $2$ 个样例。我们发现在 $d_1 = 1, d_2 = 3$ 的时候，理论上应该有如下结果：

```cpp
di = 1 : 0 1 0 0
di = 3 : 0 1 1 0
```

但是却出现了：

```cpp
di = 1 : 0 1 0 0
di = 3 : 0 1 0 0
```

发现进行该转移的应是一个下降序列，但是下降序列转移本质上只能选择一次，因为 `min(l[j], r[j]) <= last + 1` 这个条件没有满足所以没有进行转移，所以我们必须再钦定一个转移的方案，不妨考虑将 $l_i$ 多拆成 $l_i - 1$，这样就可以进行转移了。

对于上升也是同理的。

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

const int maxn = 50 + 5;
const int mod = 998244353;
typedef long long int64;
int n, m, a[maxn];

struct Matrix {
    int a[maxn][maxn];
    void clear() { memset(a, 0, sizeof(a)); }
    Matrix(void) { clear(); }
    int* operator [] (const int& x) { return a[x]; }
    const int* operator [] (const int& x) const { return a[x]; }
    Matrix operator * (const Matrix& z) const {
        Matrix res;
        for(int i = 0; i <= m; ++ i) {
            for(int j = 0; j <= m; ++ j) {
                for(int k = 0; k <= m; ++ k) {
                    res[i][j] = (res[i][j] + 1ll * a[i][k] * z[k][j]) % mod;
                }
            }
        }
        return res;
    }
}A, B;

int tot(0), totd(0);
int64 d[maxn * 6], l[maxn], r[maxn];

void ksm(int mi) {
//    printf("mi = %d\n", mi);
    while(mi) {
        if(mi & 1) A = A * B;
        mi >>= 1;
        B = B * B;
    }
}

int64 ans(0);

int Solve(int L,int R) {
//    printf("[%d, %d]\n", L, R);
    A.clear();
    tot = totd = 0;
    int64 tmp(1);
    A[0][0] = 1;
    for(int i = L; i <= R; tmp += a[i], ++ i) {
        l[++ tot] = (a[i] > 0) ? tmp + 1 : tmp - 1;
        r[tot] = tmp + a[i];
        if(tot == 1) l[tot] = 1;
        d[++ totd] = l[tot], d[++ totd] = r[tot];
        d[++ totd] = l[tot] - 1, d[++ totd] = r[tot] - 1;
        d[++ totd] = l[tot] + 1, d[++ totd] = r[tot] + 1;
//        printf("tot = %d\n", tot);
    }
    sort(d + 1, d + totd + 1);
    totd = unique(d + 1, d + totd + 1) - d - 1;
//    for(int i = 1; i <= totd; ++ i) printf("%d : %d\n", i, d[i]);
    m = tot;// !!!!
    int64 last = 0;
    int i, j;
    for(i = 1; i <= totd; ++ i) {
        if(d[i] <= last || d[i] > 1 + ans) continue;
        B.clear();
        for(j = 1; j <= tot; ++ j) if(min(l[j], r[j]) <= last + 1 && max(l[j], r[j]) >= d[i]) {
            for(int k = 0; k <= j; ++ k)
                B[k][j] = 1;
            if(l[j] >= r[j]) B[j][j] = 0;
        }
        ksm(d[i] - last);
        last = d[i];
    }
    int64 res(0);
    for(i = 0; i <= tot; ++ i) res += A[0][i];
//    printf("[%d, %d]\n", L, R);
    return res % mod;
}


signed main() {
	int i, j;
    r1(n, i);
    for(i = 1; i <= n; ++ i) {
        r1(a[i]); if(a[i] == 0) -- i, -- n;
    }
    if(n == 0) return puts("1 1"), 0;
    int64 sum(0), ssum(0);
    for(i = 0; i <= n; ++ i) {
        sum = a[i];
        ssum += a[i];
        for(j = i + 1; j <= n; ++ j) {
            sum += a[j];
            if(sum > a[i]) ans = max(ans, sum - a[i]);
        }
    }
    if(ans == 0) return printf("1 %lld\n", (1 - ssum) % mod), 0;
    int64 res(0);
    for(i = 1; i <= n; ++ i) {
        int rt = -1; int64 tmp(0), st(0);
        for(j = i; j <= n; ++ j) {
            tmp += a[j];
            if(tmp - st == ans) rt = j;
        }
        if(rt == -1) continue;
        res += Solve(i, rt), i = rt;
    }
    printf("%lld %lld\n", (ans + 1), res % mod);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
