---
title: "P4351 [CERC2015]Frightful Formula 题解"
date: 2022-4-22 14:43:00
tags:
  - 数论
categories:
  - 洛谷题解
---

[P4351 [CERC2015]Frightful Formula](https://www.luogu.com.cn/problem/P4351)

首先将原来的式子看成网格的贡献，发现 $c$ 比较难处理，我们先不管 $c$。

那么 $a, b$ 本质就是向右走一步和向下走一步的贡献。

那么对于位置 $(x, y)$ 其最终的贡献是 $\dbinom{2n - x - y}{n - x} \times a^{n - x} b^{n - y}$。

我们只需要考虑 $(1, i), (i, 1)$ 即可。

之后考虑 $c$ 的贡献怎么计算，发现位置 $\forall i, j, i \ge 2, j \ge 2$ 会产生贡献。

显然 $c$ 的贡献同上。
$$
ans = \sum_{i = 1} ^ n \binom{2n - 1 - i}{i} (a^{n - 1} b^{n - i} + a^{n - i}b^{n - 1} )  + c\sum_{i = 2} ^ n \sum_{j = 2} ^ n \binom{2n - i - j}{n - j} a^{n - i}b^{n - j}
$$
前面部分可以直接计算，考虑计算后面的部分。

后面等价于 $c\sum_{i = 0} ^ {n - 2} \sum_{j = 0} ^ {n - 2} a^i b^j \binom{i + j}{i}$，发现就是一个卷积的形式。

设 $u = n - 2, A(x) = \sum_{i = 0} ^ u a^i \frac{x^i}{i!}, B(x) = \sum_{i = 0} ^ u b^i\frac{x^i}{i!}$，答案就是 $\sum_{i = 0} ^ {2u} [x^i] i!A(x)B(x)$。

考虑进行这里提到的[经典变换](https://legendgod.ml/2022/02/28/generating-function/)。

设 $F(x) = A(x)B(x)$，我们有：
$$
\begin{aligned}
F' &= AB' + A’B \\\\
&= A \times (bB - b^{u + 1} \frac{x^u}{u!}) + B \times (aA - a^{u + 1}\frac{x^u}{u!}) \\\\
&= (a +b)F - \frac{x^u}{u!} (Ab^{u + 1} + Ba^{u + 1}) 
\end{aligned}
$$
可以直接对于上式提取系数：
$$
f(i + 1) = (a + b) f(i) - \binom{i}{u}(b^{u + 1}a^{i - u} + a^{u +1}b^{i - u})
$$
事实上我们只要递推 $i = n - 1 \sim 2n - 4$ 的部分。

对于前面的部分可以发现：
$$
\sum_{i + j = 0} ^ {n - 2} \binom{i + j}{i} a^ib^j = \sum_{i + j = 0} ^ {n - 2} (a + b) ^{i + j}
$$
可以直接进行计算。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

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

constexpr int maxn = 4e5 + 5;
constexpr int mod = 1e6 + 3;

int n, A, B, c;
int fac[maxn], finv[maxn], pwa[maxn], pwb[maxn];

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

void init(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = Inv(fac[x]);
    for(int i = x - 1; i >= 0; -- i) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
    pwa[0] = pwb[0] = 1;
    for(int i = 1; i <= x; ++ i) pwa[i] = 1ll * pwa[i - 1] * A % mod, pwb[i] = 1ll * pwb[i - 1] * B % mod;
}

int C(int a,int b) {
    if(a < 0 || b < 0 || a < b) return 0;
    return 1ll * fac[a] * finv[b] % mod * finv[a - b] % mod;
}

int f[maxn], l[maxn], t[maxn];

signed main() {
	int i, j;
    r1(n, A, B, c);
    for(i = 1; i <= n; ++ i) r1(l[i]);
    for(i = 1; i <= n; ++ i) r1(t[i]);
    init(2 * n);
    int res(0);
    for(i = 2; i <= n; ++ i) {
        long long tmp(0);
        tmp += 1ll * C(2 * n - 2 - i, n - i) * pwa[n - 1] % mod * pwb[n - i] % mod * l[i] % mod;
        tmp += 1ll * C(2 * n - 2 - i, n - i) * pwb[n - 1] % mod * pwa[n - i] % mod * t[i] % mod;
        res = (res + tmp) % mod;
    }
    {
        int now(1);
        for(i = 0; i <= n - 2; ++ i) res = (res + 1ll * c * now) % mod, now = 1ll * now * (A + B) % mod;
    }
//    printf("res = %d\n", res);
    f[n - 2] = ksm(A + B, n - 2);
    int u = n - 2;
    for(i = n - 2; i < 2 * n - 4; ++ i) {
        int tmp = ( 1ll * pwb[u + 1] * pwa[i - u] + 1ll * pwa[u + 1] * pwb[i - u] ) % mod;
        f[i + 1] = 1ll * (A + B) * f[i] % mod - 1ll * C(i, u) * tmp % mod;
        f[i + 1] = (f[i + 1] + mod) % mod;
    }
    for(i = n - 1; i <= 2 * u; ++ i) res = (res + 1ll * c * f[i]) % mod;
    printf("%d\n", res);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```



