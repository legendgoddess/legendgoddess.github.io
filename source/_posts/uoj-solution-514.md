---
title: "[UR #19]通用测评号"
date: 2021-11-16 18:50:00
tags:
  - 数论
  - 多项式
  - 生成函数
categories:
  - UOJ题解
---

<h3><center>#514. 【UR #19】通用测评号</center></h3>

> [#514. 【UR #19】通用测评号](https://uoj.ac/problem/514)

一个小 $\tt trick$：

- 对于这样的 $a$ 限制，我们可以无视它，但是代价是无穷项。

  > 感觉感性证明更好理解：对于一个位置多的操作，我们直接不做即可，直接去考虑下一个操作，显然答案是不变的。

考虑填充的结束条件，就是恰好有一个燃料舱是 $b$ 单位的。

而且因为期望是线性的，我们直接对于每个燃料舱进行计算概率即可。最终的答案就是 $(n - 1) \times P$。

> 我们将 $z = 1$ 带入即可。

那么我们考虑一下这个 $P$ 是怎么计算的，我们不妨考虑拿出最后一个是 $b$ 的燃料舱，那么就是 $n$。为了方便我们设 $f(x)$ 表示进行了 $x$ 次操作的概率函数，也就是 $\dfrac{z^x}{x!n^x}$。

显然最后一个仓就是 $f(B - 1) \times \frac{1}{n} \times n = f(B - 1)$。

之后对于其他仓我们只要保证至少一个是符合条件即可，那么就是 $(e^x - f(A - 1)) \times (e^x - f(B - 1)) ^{n - 2}$。

所以得到：$P = f(B - 1)\times(e^z - f(A - 1)) \times (e^z - f(B - 1)) ^{n - 2}$

发现 $e^z$ 实际上是无限项的，我们考虑将 $e^{\frac{az}{n}} \times \dfrac{z^b}{b!}\times C$ 进行展开。

别忘了我们最后算的是 $ogf$ 所以之后还要还要将其转化成 $ogf$。

我们展开一手（不用管 $C$，毕竟是常数）：
$$
\begin{aligned}
&\sum_{i \ge 0} \frac{z^{b + i}a^i}{i!b!n^i} \\
=&z^b \sum_{i \ge 0} \binom{i + b}{i}\frac{a^iz^i}{n^i}\\
=&\frac{z^b}{(1 - \frac{i}{n}z)^{b + 1}}
\end{aligned}
$$
这里补充一下：
$$
\frac{1}{(1 - z)^b} = \sum_{i \ge 0} \binom{i - (-b) - 1}{i} z^i
$$
这个就是上指标翻转（后面其实有 $(-1)^{2i}$）。

我们对于卷积的部分直接进行 $dft$ 后点乘即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

namespace Legendgod {

namespace Read {
//    #define Fread
    #ifdef Fread
    const int Siz = (1 << 21) + 5;
    char buf[Siz], *iS, *iT;
    #define gc() (iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iT == iS ? EOF : *iS ++ ) : *iS ++)
    #define getchar gc
    #endif // Fread

    template <typename T>
    void r1(T &x) {
        x = 0; int f(1); char c(getchar());
        for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
        for(; isdigit(c); c = getchar()) x = (x * 10) + (c ^ 48);
        x *= f;
    }
    template <typename T, typename...Args>
    void r1(T &x, Args&...arg) {
        r1(x), r1(arg...);
    }

}

using namespace Read;

const int maxn = 2e5 + 5;
const int maxm = 250 + 5;
constexpr int mod = 998244353;
constexpr int G = 3, invG = 332748118;

int rev[maxn];

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

int lim, len;
int w[2][maxn];

void FFT(int *A,int opt = 1) {
    for(int i = 0; i < lim; ++ i) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1; mid < lim; mid <<= 1) {
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            for(int k = 0; k < mid; ++ k) {
                int x = A[j + k], y = 1ll * w[(opt != 1)][mid + k] * A[j + k + mid] % mod;
                A[j + k] = (x + y) % mod;
                A[j + k + mid] = (x - y + mod) % mod;
            }
        }
    }
    if(opt != 1) {
        int z = ksm(lim, mod - 2);
        for(int i = 0; i < lim; ++ i) A[i] = 1ll * A[i] * z % mod;
    }
}

int fac[maxn], inv[maxn], invf[maxn];

void init(int x) {
    fac[0] = inv[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    invf[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) invf[i] = 1ll * invf[i + 1] * (i + 1) % mod;
    for(int i = 1; i <= x; ++ i) inv[i] = 1ll * fac[i - 1] * invf[i] % mod;
}

int n, A, B;
int pw[maxn];

int C(int a,int b) { return a < b ? 0 : 1ll * fac[a] * invf[b] % mod * invf[a - b] % mod; }

int f1[maxn], f2[maxn];

int f[maxm][maxm * maxm];

signed main() {
    int i, j;
    init(maxn - 5);
    r1(n, A, B);
    int up = (n - 1) * B + A - 1;
    lim = 1, len = 0;
    while(lim <= up) lim <<= 1, ++ len;
    for(i = 0; i < lim; ++ i) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
    for(int mid = 1; mid < lim; mid <<= 1) {
        int wn = ksm(G, (mod - 1) / (mid << 1)), w1 = 1;
        for(int j = 0; j < mid; ++ j)
            w[0][mid + j] = w1, w1 = 1ll * w1 * wn % mod;
    }
    for(int mid = 1; mid < lim; mid <<= 1) {
        int wn = ksm(invG, (mod - 1) / (mid << 1)), w1 = 1;
        for(int j = 0; j < mid; ++ j)
            w[1][mid + j] = w1, w1 = 1ll * w1 * wn % mod;
    }
    pw[0] = 1;
    for(i = 1; i <= up; ++ i) pw[i] = 1ll * pw[i - 1] * inv[n] % mod;
    for(i = 0; i < B; ++ i) f1[i] = (mod - 1ll * pw[i] * invf[i] % mod) % mod;
    for(i = 0; i < A; ++ i) f2[i] = (mod - 1ll * pw[i] * invf[i] % mod) % mod;
    FFT(f1, 1), FFT(f2, 1);
    for(i = 0; i < lim; ++ i) f[0][i] = 1;
    for(i = 1; i <= n - 2; ++ i) {
        for(j = 0; j < lim; ++ j) f[i][j] = 1ll * f[i - 1][j] * f1[j]% mod;
    }
    for(i = 0; i <= n - 2; ++ i) {
        int tmp = C(n - 2, i);
        for(j = 0; j < lim; ++ j) f[i][j] = 1ll * f[i][j] * tmp % mod;
    }
    for(i = n - 2; i >= 0; -- i) {
        for(j = 0; j < lim; ++ j) f[i + 1][j] = (f[i + 1][j] + 1ll * f[i][j] * f2[j] % mod) % mod;
    }
    for(i = 0; i <= n - 1; ++ i) FFT(f[i], - 1);
    int ans(0);
    for(i = 0; i <= n - 1; ++ i) {
        int tmp1 = 1ll * invf[B - 1] * pw[B - 1] % mod, tmp2 = 1ll * n * inv[i + 1] % mod;
        for(j = 0; j < B - 1; ++ j) tmp1 = 1ll * tmp1 * tmp2 % mod;
        for(j = 0; j <= up; ++ j) {
            tmp1 = 1ll * tmp1 * tmp2 % mod;
            ans = (ans + 1ll * f[i][j] * tmp1 % mod * fac[j + B - 1] % mod) % mod;
        }
    }
    ans = 1ll * ans * (n - 1) % mod;
    printf("%d\n", ans);
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }

```



