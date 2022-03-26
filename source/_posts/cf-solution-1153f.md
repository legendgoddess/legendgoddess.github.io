---
title: "CF1153F Serval and Bonus Problem 题解"
date: 2022-3-26 19:57:00
tags:
  - Dp
categories:
  - CF题解
---

[Serval and Bonus Problem - 洛谷](https://www.luogu.com.cn/problem/CF1153F)

> 最近有 $\tt ARC\ F$ 题竟然是相似题，好吧其实也没有那么相似，但是第一步转换是经典的。

首先根据期望线性，求一条线段的期望就是求区间内所有点的期望的和。

之后考虑既然是点那问题就简单了，直接考虑每条线段的相对位置就行了，也就是考虑 $(2n + 1)$ 个点，随机排列被钦定的点 $X$ 被 $\tt K$ 条线段覆盖的期望。

直接考虑进行 $\tt Dp$，考虑去对点进行匹配，设考虑到第 $i$ 个点，还有 $j$ 个点没有右端点，当前位置有没有被选择的方案数。

$$
\begin{aligned}
f(i, j, k) &\to f(i + 1, j + 1, k) \\\\
f(i, j, k) \times j &\to f(i + 1, j - 1, k) \\\\
f(i, j, 0) &\to f(i + 1, j, 1), j \ge K
\end{aligned}
$$

当然我们最后的时候还要考虑线段的顺序，点的顺序，两个端点的顺序，还要把之前是考虑长度为 $1$ 现在变成 $l$ 了。

> 我们可以发现两个点重合的概率是趋近于 $0$ 的。

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

const int maxn = 2e3 + 5;
constexpr int mod = 998244353;
int n, L, K;
int fac[maxn << 1];
int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

int Inv(int x) { return ksm(x, mod - 2); }

int f[maxn << 1][maxn][2];

void Add(int &x,int c) {
    x = (x + c) % mod;
}

signed main() {
	int i, j;
    r1(n, K, L);
    f[0][0][0] = 1;
    for(i = 0; i <= 2 * n + 1; ++ i) {
        for(j = 0; j <= min(n, i); ++ j) {
            for(int k = 0; k <= 1; ++ k) {
                Add(f[i + 1][j + 1][k], f[i][j][k]);
                if(j > 0) Add(f[i + 1][j - 1][k], 1ll * f[i][j][k] * j % mod);
                if(k == 0 && j >= K) Add(f[i + 1][j][1], f[i][j][0]);
            }
        }
    }
    fac[0] = 1;
    for(i = 1; i <= 2 * n + 1; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    int ans = 1ll * Inv(fac[2 * n + 1]) * ksm(2, n) % mod * L % mod * f[2 * n + 1][0][1] % mod * fac[n] % mod;
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

当然你会发现这个题目被加强了，如果说 $O(n ^ 2)$ 是不能过的呢？

但是很遗憾，你是不能再对 $\tt Dp$ 进行优化了，因为状态数已经过大了。

我们考虑使用一些更加数学的方法，我们还是考虑区间为 $[0, 1]$ 的情况，对于每个点求概率（期望）。

$$
\begin{aligned}
p_x = \sum_{i = k} ^ n \binom{n}{i} (2x(1 - x)) ^ i\times (1 - (2x(1 - x))^i
\end{aligned}
$$

其中 $x$ 表示点的位置，后面就是考虑线段的情况。

$$
\begin{aligned}
&\int_{0}^1 p_x dx\\\\
=& \int_{0}^ 1 \sum_{i = k} ^ n \binom{n}{i} (2x(1 - x)) ^ i\times (1 - (2x(1 - x))^i\\\\
=& \sum_{i = k} ^ n \binom{n}{i} \int_{0}^1 (2x(1 - x)) ^ i \times (1 - (2x(1-x)))^{n - i}dx\\\\ 
=& \sum_{i = k} ^ n \binom{n}{i} \sum_{j = 0} ^ {n - i} (-1)^j \binom{n - i}{j} 2^{i + j} B(i + j + 1, i + j + 1)\\\\
\end{aligned}
$$

其中 $B(x, y) = \frac{(x-1)!(y-1)!}{(x + y - 1)!}$。

考虑对于前面的东西进行变化，可以发现 $\binom{n}{i}\binom{n-i}{j} = \binom{n}{i+j}\binom{i+j}{j}$。

可以枚举 $i + j$。

$$
\begin{aligned}
\sum_{i = k} ^ n \binom{n}{i} 2^i B(i + 1, i + 1)  \sum_{j = 0} ^ {i - k} (-1)^j\binom{i}{j}
\end{aligned}
$$

众所周知后面的东西只变化了下指标，我们是可以直接进行求和的。

可以得到 $\sum_{j = 0} ^ {i - k} (-1)^j \binom{i}{j} = (-1)^{i - k} \binom{i - 1}{i - k}$。

> 二项式拆开来就行了。

最终得到的答案是可以 $O(n)$ 计算的。

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

const int maxn = 1e6 + 5;
constexpr int mod = 998244353;
int n, L, K;
int fac[maxn], finv[maxn], pw2[maxn];
int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}
void init(int x) {
    fac[0] = pw2[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
    for(int i = 1; i <= x; ++ i) pw2[i] = 2ll * pw2[i - 1] % mod;
}

int C(int a,int b) {
    if(a < b || a < 0 || b < 0) return 0;
    return 1ll * fac[a] * finv[b] % mod * finv[a - b] % mod;
}

int B(int a,int b) {
    if(a < 0 || b < 0) return 0;
    return 1ll * fac[a - 1] * fac[b - 1] % mod * finv[a + b - 1] % mod;
}

signed main() {
	int i, j;
    init(maxn - 5);
    r1(n, K, L);
    int ans(0);
    for(i = K; i <= n; ++ i) {
        int op = ((i - K) & 1) ? mod - 1 : 1;
        ans = (ans + 1ll * C(n, i) * pw2[i] % mod * B(i + 1, i + 1) % mod * op % mod * C(i - 1, i - K) % mod) % mod;
    }
    ans = 1ll * ans * L % mod;
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
