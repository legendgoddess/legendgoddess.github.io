---
title: "再做 CF1603E A Perfect Problem 题解"
date: 2022-4-5 18:46:00
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1603E - Codeforces](https://codeforces.com/problemset/problem/1603/E)

> 虽然是再论但是是一点题解的印象都无了。

首先从小到大开始考虑，对于这样的一个序列事实上我们可以任意排列，答案就是：

$$
\frac{n!}{\prod_{i} c_i!}
$$

其中 $c_i$ 表示一个数重复出现的次数。

发现做不下去了，考虑找找性质。

不妨考虑区间 $[l, r]$ 那么需要满足 $a_l \times a_r \ge sum_r - sum_{l - 1}$。

考虑对于同一个 $r$，$l$ 越小限制越强。

- 对于该序列只要前缀是合法的答案一定是合法的。

直接考虑 $\tt Dp$ 需要枚举第一个数，然后记录前缀和。

复杂度是 $O(n\times n^3\times n)$ 是过不去的。

考虑是否还有性质，不是很好找，那么考虑压缩状态，如果考虑前缀和第一个数不论转移的复杂度就已经是 $O(n^4)$ 了。

所以考虑能不能少枚举一些东西，前缀和是不能省掉的，考虑能否倒叙枚举，发现如果 $a_n \le n - 1$ 就去世了。所以 $a_n = n, n + 1$。

可以省略掉一维了，但是算上转移之后还是 $O(n ^ 4 \ln n)$ 的。

> 有一个枚举相同数的调和级数。

- 我们发现对于 $n$ 其实可以看成任意一个数，所以我们有 $a_i \ge i$ 的限制。

但是如果 $a_i = i$ 限制又是不成立的，考虑列方程：

$$
(i-1)x + i \le ix
$$

可以得到：

$$
i \le x
$$

又因为 $x \le i$ 所以发现如果出现 $a_i = i$ 我们必须有 $\forall j \le i, a_j = i$。

那么我们只需要考虑 $a_n = n + 1$ 的情况，稍微打表发现状态是貌似不是很多，因为 $a_1$ 的限制是比较明显的。

不妨考虑枚举 $a_1 = a_n - x$，之后进行 $\tt dfs$ 记忆化。

考虑计算大概的下界：

$$
(n + 1 - x) (n + 1) \ge (n + 1 - x) ^2 + \frac{(n + 1 - x + 1 +1) + (n + 1)}{2} \times (x - 1)
$$

解得：

$$
x \le \frac{-6 + \sqrt{25 + 8n}}{2}
$$

带入得到 $max_x = 17$ 我们取 $18$ 保险一点，复杂度是 $O(n^3\sqrt{n}\ln n)$。

---

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
    int ans(0);
    int up;
    if(8 * n < 25) up = n;
    else up = sqrt(8 * n - 25) / 2 + 1;
//    printf("up = %d\n", up);
    for(int lim = 0; lim <= up; ++ lim) {
        memset(vis, 0, sizeof(vis));
        a1 = n + 1 - lim;
        int x = dfs(0, 0, 0, lim);
//        if(x == 0) break;
        ans = (ans + x) % mod;
    }
    ans = 1ll * ans * fac[n] % mod;
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }
```


