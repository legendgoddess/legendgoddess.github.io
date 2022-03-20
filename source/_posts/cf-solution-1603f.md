---
title: "CF1603F October 18, 2017 题解"
date: 2022-3-13 20:57:20
tags:
  - Dp
  - 生成函数
categories:
  - CF题解
---

[Problem - 1603F - Codeforces](https://codeforces.com/problemset/problem/1603/F)

> 看不懂题解，自己推一下。

发现直接进行操作是不方便的，我们会想到线性基，那么我们考虑通过基底进行计算。

当 $x = 0$ 的时候也就是意味着选择的位置都是线性无关的，我们考虑一个一个进行填充可以得到 $\prod_{i = 1} 2^k - 2^{i - 1}$。

> 显然 $n > k$ 的时候根据向量基底的性质，不可能产生线性无关的，直接为 $0$ 即可。

如果 $x \ne 0$ 我们考虑进行 $\tt Dp$，设 $f(i, r)$ 表示考虑到了 $i$ 个数，矩阵的秩是 $r$，转移方程考虑是否通过之前的位置进行表示。

我们考虑将限制 $x$ 加入进来，我们考虑任何一个数都是不能被 $x$ 所组成的，这样就是 $(n + 1) \times k$ 的矩阵了。

> 考虑 $a \oplus b = c \to a = b\oplus c$。

$$
f(i, r) = f(i - 1, r) \times 2^{r - 1} + f(i - 1, r - 1) \times (2^k - 2^{r - 1})
$$

考虑前者是因为如果是一个由之前基底构成的量，肯定不能是 $x$ 构成的，如果成为了基底，需要和之前所有的位置都不同，也就是最开始说的方案，复杂度是 $O(n ^ 2)$ 的。

我们发现转移比较特别是两个两个进行转移的，我们考虑对于一种下标进行优化，设 $F_r(z) = \sum_{i \ge 0} f(i, r) z^i$。

直接进行转移可以得到 $F_r(z) = 2^{r - 1}F_r(z)z + (2^k - 2^{r - 1})F_{r - 1}(z)z$，通过移项能够得到 $F_r(z) = (2^k - 2^{r - 1})F_{r - 1}(z)z \times \frac{1}{1 - 2^{r - 1}z}$。

这个东西好像可以直接找到通向。

$$
F_r(z) = z^r \prod_{i = 2} ^ r (2^k - 2^{i - 1}) \times  \prod_{i = 1} ^ r \frac{1}{1 - 2^{i - 1}z}
$$

> 注意 $i = 1$ 的时候本质上只有 $f(1, 1) = 1$ 这一个方案。

我们发现这个东西不是很好看，我们将 $1$ 的部分也算上拿出来。

$$
F_r(z) = \frac{1}{2^k - 1}z^r \prod_{i = 1} ^ r (2^k - 2^{i - 1}) \times  \prod_{i = 1} ^ r \frac{1}{1 - 2^{i - 1}z}
$$

我们的答案是 $\sum_{r = 1} ^ k [z^{n + 1}] F_r(z)$。

考虑将后面有 $z$ 的部分求出贡献，设 $G_r(z) = \prod_{i = 1} ^ r \frac{1}{1 - 2^{i - 1} z}$。

发现求导不是很方便，考虑 $2^{i - 1}$ 我们不妨让 $z \to 2z$。

发现展开之后有很多项本质是相同的，只有第一项和最后一项是不同的，所以我们可以得到方程 $(1 - z)G_r(z) = (1 - 2^rz)G_r(2z)$。

我们直接进行展开可以发现 $G_r(z) - zG_r(z) = G_r(2z) - 2^rzG_r(2z)$。

设 $g_n = [z^n]G_r(z)$ 显然可以发现 $[z^n]G_r(2z) = 2^ng_n$，可以得到递推方程：

$$
\begin{aligned}
g_n + g_{n - 1} &= 2^ng_n - 2^{r + n - 1}g_{n - 1} \\
g_n &= \frac{2^{r + n - 1} - 1}{2^n - 1} g_{n - 1} \\
g_n &= \prod_{i = 1} ^ n \frac{2^{r + i - 1} - 1}{2^i - 1} \\
g_1 &= 1
\end{aligned}
$$

所以原式可以变成：

$$
[z^{n + 1}]F_r(z) = \frac{1}{2^k - 1}\prod_{i = 1} ^ r (2^k - 2^{i - 1}) \times  \prod_{i = 1} ^ {n + 1 - r} \frac{2^{r + i - 1} - 1}{2^i - 1}
$$

我们再看看什么情况是不可能的，显然如果 $r > K$ 是不行的，因为我们除掉了考虑不可行的位置，另外的位置已经可以表示出所有的情况了，显然是不行的。

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

const int maxn = 1e7 + 5;
constexpr int mod = 998244353;

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}
int pw[maxn], ipw[maxn], ipw1[maxn], pw1[maxn];
void init(int x) {
    pw[0] = pw1[0] = 1;
    for(int i = 1; i <= x; ++ i) pw[i] = 2ll * pw[i - 1] % mod, pw1[i] = 1ll * pw1[i - 1] * (pw[i] - 1) % mod;
    ipw[x] = ksm(pw[x], mod - 2);
    ipw1[x] = ksm(pw1[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) {
        ipw[i] = 2ll * ipw[i + 1] % mod;
        ipw1[i] = 1ll * ipw1[i + 1] * (pw[i + 1] - 1) % mod;
    }
}

int n, K, X, tmp[maxn];

void Solve() {
    int i, j;
    r1(n, K, X);
    if(X == 0) {
        if(n > K) return puts("0"), void();
        int res(1);
        for(int i = 1; i <= n; ++ i) res = 1ll * res * (pw[K] - pw[i - 1]) % mod;
        res = (res + mod) % mod;
        printf("%d\n", res);
        return ;
    }
    int ans(0), res(1), up(min(n + 1, K));
    tmp[up] = ksm(2, n + 1);
    for(int i = up - 1; i >= 0; -- i) tmp[i] = 1ll * tmp[i + 1] * ipw[1] % mod;
//    printf("res = %d\n", res);
    for(int r = 1; r <= up; ++ r) {
        res = 1ll * res * (pw[K] - pw[r - 1]) % mod;
        ans = (ans + res) % mod;
//        printf("res = %d\n", res);
        res = 1ll * res * (tmp[up - r] - 1) % mod * ipw1[r] % mod * pw1[r - 1] % mod;
    }
//    for(int r = 1; r <= up; ++ r) {
//        int tmp = 1;
//        for(int j = 1; j <= r; ++ j) tmp = 1ll * tmp * (pw[K] - pw[j - 1]) % mod;
//        for(int j = 1; j <= n + 1 - r; ++ j) tmp = 1ll * tmp * ksm(pw[j] - 1, mod - 2) % mod * (pw[r + j - 1] - 1) % mod;
//        printf("tmp = %d\n", tmp);
//        ans = (ans + tmp) % mod;
//    }
    ans = (ans + mod) % mod;
    ans = 1ll * ans * ipw1[K] % mod * pw1[K - 1] % mod;
    printf("%d\n", ans);
}
/*
1
3 2 3
*/
signed main() {
	int i, j, T;
    init(maxn - 5);
//    printf("pw[5] = %d\n", 1ll * pw[5] * ipw[5] % mod);
    r1(T); while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }

```
