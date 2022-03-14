---
title: CF1603E A Perfect Problem 题解
date: 2022-3-13 20:57:20
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1603E - Codeforces](https://codeforces.com/problemset/problem/1603/E)

> 找找性质的好玩题。

首先考虑顺序对于题目的要求是没有关系的

- 对于一个**排好序**的序列，如果其符合条件那么其排列也肯定符合条件。

考虑对于这个排好序的序列进行操作，可以得到如果这个序列的每一个前缀都符合条件那么这个序列肯定符合条件。

既然是排好序了我们考虑对于每一个数是否有限制，限制肯定是存在的，如何找到呢？

明显发现对于 $\{1, 1,1, 1, 1\}$ 是不符合条件的，考虑 $\sum_{i = 1} ^ n a_i \le a_1 \times a_n$ 的限制。

$$
\begin{aligned}
\sum_{i = 1} ^ n (a_i - a_1) \le a_1 \times (a_n - n) \\\\ 
\end{aligned}
$$

- 当 $a_n = n$ 时，$\forall i \le n, a_i = i$。

- $a_n \ge n$。

我们考虑 $a_n \ge n+ 1$ 的情况，考虑边界 $a_n = n+ 1$，同样是上面的式子可以得到 $\sum_{i = 1} ^ n (a_i - a_1) \le a_1$，很明显这个是最强的限制条件。

- 如果一个数列的 $a_n = n +1$ 而且上述条件满足，该序列肯定是合法的。

我们考虑进行 $\tt Dp$，设 $f(i, sum, k)$ 表示当前考虑到第 $i$ 个数，前缀和是 $sum$，当前位置选择的是 $k$ 的方案数。

我们考虑将排列的方案数一起算进去，也就是考虑每次 $\times \frac{1}{cnt!}$ 之后再乘上 $n!$ 即可。

转移的话我们考虑对于下一个数 $k + 1$，考虑其出现的次数，也就是可以得到转移方程。

$$
f(i, sum, k) = \sum_{j + i \le n} \frac{1}{j!} \times f(i + j, sum + j \times k, k + 1) 
$$

我们考虑计数 $b_i = a_i - a_1$，我们需要满足如下条件：

- $0 \le b_i \le n - a_1 + 1$

- $\sum b_i \le a_1$

除此之外我们还要考虑之前限制了 $a_i \ge i$，考虑对于 $i < a_1$ 那么我们需要有 $n + 1 \ge a_i \ge a_1$，如果 $i \ge a_1$ 我们需要有 $n + 1 \ge a_i \ge i + 1$。

- 对于 $a_i \ge i + 1$ 的限制本质上可以看成有 $1$ 个数大于 $n + 1 - a_1$，有 $2$ 个数大于 $n + 1 - a_1 - 1$，也就是说有 $i$ 个数要大于 $n + 2 - a_1 - i$。

我们可以通过这个判断状态是否合法即可。

但是我们发现这个复杂度 $O(n^4\ln n)$ 还是不能接受，我们考虑还有什么地方是可以优化的，我们发现我们不得不枚举 $a_1$ 的范围，但是因为**数个数的限制**事实上能取到的位置还是没有那么多的。

考虑到如果 $a_1$ 已经没有合法的数列了，那么 $a_1 - 1$ 也肯定没有，我们直接进行剪枝即可。

> ```cpp
>     for(int lim = 0; lim <= n; ++ lim) {
>         memset(vis, 0, sizeof(vis));
>         a1 = n + 1 - lim;
>         int x = dfs(0, 0, 0, lim);
>         if(x == 0) break;
>         ans = (ans + x) % mod;
>     }
> ```

或者我们来算一下下界，考虑对于 $(1 \le i \le n - a_1)$ 我们有 $i$ 个数大于等于 $n + 2 - i - a_1$，我们不妨让其全部都等于这个值。

那么符合条件的情况就是：

$$
\dfrac{(n - a_1) \times (n + 3 - a_1)}{2} \le x
$$

考虑解方程得到 $n + \frac{1 - \sqrt{8n - 5} } {2} \le a_1 \le n + 1$。

考虑我们实际枚举的时候是从 $a_n$ 开始枚举，所以我们只需要枚举到 $n + 1 - n - \frac{1 - \sqrt{8n - 5} } {2}$ 即可，也就是 $O(\sqrt n)$ 级别的，所以我们的复杂度就是 $O(n^{\frac{7}{2}}\ln n)$ 可以通过此题。

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

const int maxn = 200 + 5;

int n, mod;
int fac[maxn], finv[maxn];
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
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
}

int vis[maxn][maxn][50], f[maxn][maxn][50];
int a1;

int dfs(int dep,int sum,int K,int lim) {
    if(vis[dep][sum][K]) return f[dep][sum][K];
    vis[dep][sum][K] = 1;
    int& ret = f[dep][sum][K]; ret = 0;
    if(K < lim && dep < K) return 0;
    if(K == lim) {
        if(dep < n) ret = finv[n - dep];
        return ret;
    }
    int nx = n + 1 - K - a1;
    for(int j = 0; dep + j <= n && j * nx + sum <= a1; ++ j)
        ret = (ret + 1ll * dfs(dep + j, j * nx + sum, K + 1, lim) * finv[j] % mod) % mod;
    return ret;
}

signed main() {
	int i, j;
    r1(n, mod);
    init(n);
    int ans(0), up;
    if(8 * n < 25) up = n;
    else up = sqrt(8 * n - 25) / 2 + 1;
    for(int lim = 0; lim <= up; ++ lim) {
        memset(vis, 0, sizeof(vis));
        a1 = n + 1 - lim;
        ans = (ans + dfs(0, 0, 0, lim)) % mod;
    }
    ans = 1ll * ans * fac[n] % mod;
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }

```
