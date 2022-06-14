---
title: "UOJ 269 [清华集训2016] 如何优雅地求和 题解"
date: 2022-6-13 19:55:00
tags:
  - 斯特林数
  - 生成函数
categories:
  - UOJ题解
---

## [UOJ 269 [清华集训2016] 如何优雅地求和](https://uoj.ac/problem/269)

#### **题意：**

给定一个函数 $f(x)$，求出 $Q = \sum_{k = 0} ^ n f(k) \binom{n}{k} x^k (1 - x)^{n - k}$。

给定了 $f, n, x$。

---

可以发现给定了 $x$ 之后后面的两项可以看成常数但是又不能完全看成常数，但是可以 $O(1)$ 计算的，不妨设系数为 $c_i$。

那么就是求 $Q = \sum_{k = 0} ^ n f(k) \binom{n}{k} c_i$。

考虑拆开来看:

$$
\begin{aligned}
&\sum_{k = 0} ^ n \sum_{j = 0} ^ m k^j \binom{n}{k} c_k \\\\
=& \sum_{k  =0}^ n c_k \binom{n}{k} \sum_{j = 0} ^ m k^j \\\\
\end{aligned}
$$

考虑后面的部分怎么操作：

显然后面部分为 $\frac{k^{m + 1} - 1}{k - 1}$。

可以是很遗憾的一点就是 $n$ 巨大，只能通过一些部分分。

发现这个 $m$ 好像可以操作一下，考虑提出 $m$。

如果不考虑 $k^j$ 的话可以直接二项式定理。

考虑拆掉 $k$。
$$
\begin{aligned}
& \sum_{j = 0} ^ m \sum_{k = 0} ^ n k^j \binom{n}{k} x^k (1 - x) ^ {n - k} \\\\
=& \sum_{j = 0} ^ m \sum_{k = 0} ^ n \binom{n}{k} x^k (1 -x) ^{n - k} \sum_{z = 0} ^ j 
\begin{Bmatrix} j \\ z\end{Bmatrix}
k^{\underline{z}} \\\\
=& \sum_{j = 0} ^ m \sum_{z = 0} ^ j 
\begin{Bmatrix} j \\ z\end{Bmatrix} n^{\underline{z}} x^z \sum_{k = 0} ^ n x^{k - z} (1 -x) ^{n - k}  \binom{n - z}{k - z} \\\\
=& \sum_{j = 0} ^ m \sum_{z = 0} ^ j 
\begin{Bmatrix} j \\ z\end{Bmatrix} n^{\underline{z}} x^z \sum_{k = 0} ^ {n - z} x^{k} (1 -x) ^{n - k - z}  \binom{n - z}{k} \\\\
=& \sum_{j = 0} ^ m a_j \sum_{z = 0} ^ j x^z n^{\underline{z}}
\begin{Bmatrix} j \\ z\end{Bmatrix} 
\end{aligned}
$$
考虑将右边的东西展开：
$$
\begin{aligned}
& \sum_{j  =0} ^ m a_i \sum_{z = 0} ^ j \binom{n}{z} x^z \sum_{s = 0} ^ z
(-1)^{z - s} \binom{z}{s} s^j  \\\\
=& \sum_{z = 0} ^ m \binom{n}{z} x^z \sum_{s = 0} ^ z (-1) ^ {z - s} \binom{z}{s} \sum_{j = z} ^ m a_j s^j \end{aligned}
$$
$\tt Trick:$ 对于斯特林数的公式当 $i < j$ 的时候也适用，所以后面可以直接变成 $j = 0$，那么后面就是点值了。

直接进行操作即可。

发现前面的东西就是一个卷积，直接求出来即可。

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
#define For(a, b, c) for(int a = (b); a <= (c); ++ a)
#define uFor(a, b, c) for(int a = (b); a < (c); ++ a)
#define Pfor(a, b, c) for(int a = (b); a <= (c); ++ a, puts(""))
#define Forp(a, b, c) for(int a = (b); a <= (c); ++ a, (a == (c + 1) ? puts("") : 0))
#define Dor(a, b, c) for(int a = (b); a >= (c); -- a)
#define uDor(a, b, c) for(int a = (b); a > (c); -- a)
#define Pdor(a, b, c) for(int a = (b); a >= (c); -- a, puts(""))
#define Dorp(a, b, c) for(int a = (b); a >= (c); -- a, (a == (c - 1) ? puts("") : 0))
#define Por(a) printf("%s = %d\n", #a, a)
#define Rpor(a, b) printf("%s, %s = %d, %d\n", #a, #b, a, b)
#define Tor(a, b) printf("%s = %d : %d\n", #a, a, b)
#define Sbg puts("\nBegin")
#define Sed puts("End\n")
const int maxn = 2e5 + 5, mod = 998244353, G = 3, invG = 332748118;
int n, m, rev[maxn], lim, len;

void getrev(int x) {
    for(lim = 1, len = 0; lim <= x; lim <<= 1, ++ len);
    For(i, 0, lim) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
}

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}
int fac[maxn], finv[maxn];
void init(int x) {
    fac[0] = 1;
    For(i, 1, x) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = ksm(fac[x], mod - 2);
    Dor(i, x - 1, 0) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
}
int a[maxn];

void NTT(int *A,int op = 1) {
    For(i, 0, lim - 1) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1; mid < lim; mid <<= 1) {
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            int w = 1, wn = ksm(op == 1 ? G : invG, (mod - 1) / c);
            for(int k = 0; k < mid; ++ k) {
                int x = A[j + k], y = 1ll * A[j + k + mid] * w % mod;
                A[j + k] = (x + y) % mod;
                A[j + k + mid] = (x - y + mod) % mod;
                w = 1ll * w * wn % mod;
            }
        }
    }
    if(op != 1) {
        int z = ksm(lim, mod - 2);
        For(i, 0, lim - 1) A[i] = 1ll * A[i] * z % mod;
    }
}

signed main() {
	int i, j, x;
    r1(n, m, x);
    For(i, 0, m) r1(a[i]);
    init(m);
    static int sx[maxn], sy[maxn];
    memset(sx, 0, sizeof(sx)), memset(sy, 0, sizeof(sy));
    For(i, 0, m) {
        sx[i] = 1ll * a[i] * finv[i] % mod;
        sy[i] = (i & 1 ? mod - finv[i] : finv[i]);
    }
    getrev(m << 1);
    NTT(sx), NTT(sy);
    For(i, 0, lim - 1) sx[i] = 1ll * sx[i] * sy[i] % mod;
    NTT(sx, -1);
    For(i, 0, m) sx[i] = 1ll * sx[i] * fac[i] % mod;
//    Forp(i, 0, m) printf("%d ", sx[i]);
    int sp(1), ans(0), pwx(1);
    For(i, 0, min(n, m)) {
        ans = (ans + 1ll * sp * pwx % mod * sx[i] % mod) % mod;
        sp = 1ll * sp * (n - i) % mod * ksm(i + 1, mod - 2) % mod;
        pwx = 1ll * pwx * x % mod;
//        printf("ans = %d\n", ans);
    }
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```

---

其实这题还有一种解法，我们考虑最后的式子，如果你和我一样没有学过转下降幂那么就一起看看：

$f(x)$ 的下降幂表示。

考虑多项式 $g(x)$ 为其下降幂表示：
$$
\sum_{i = 0} ^ n a_i \sum_{j = 0} ^ i \begin{Bmatrix}i \\ j\end{Bmatrix}  x^{\underline{j}} 
$$
交换之后就是 $\sum_{j = 0} ^ n x^{\underline{j}}\sum_{i = j} ^ n a_i \begin{Bmatrix} i \\ j  \end{Bmatrix} $。

> 对于形式要敏感。

所以说人话就是 $f(x)$ 的某个下降幂表示。

那么我们对于 $f(x)$ 的点值表示如何快速求出其下降幂的表示。

显然最终的答案就是： $\sum_{z = 0} ^ m x^z n^{\underline{z}} g_z$。

直接的想法就是拉格朗日差值，但是直接暴力差值貌似是不行的。

考虑其点值的 $\tt egf$。
$$
\begin{aligned}
& \sum_{i = 0} ^ m \frac{f(i)}{i!} x^i \\\\
=& \sum_{i = 0} ^ m \frac{x^i}{i!} \sum_{j = 0} ^ m i^{\underline{j}} g_j \\\\
=& \sum_{j = 0} ^ m g_j \sum_{i = 0} ^ m \frac{x^i}{(i - j)!}
\end{aligned}
$$
显然这个东西就是 $G$ 和 $e^x$ 的卷积，我们有 $G e^x = F$ 所以 $G = F e^{-x}$。

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
#define For(a, b, c) for(int a = (b); a <= (c); ++ a)
#define uFor(a, b, c) for(int a = (b); a < (c); ++ a)
#define Pfor(a, b, c) for(int a = (b); a <= (c); ++ a, puts(""))
#define Forp(a, b, c) for(int a = (b); a <= (c); ++ a, (a == (c + 1) ? puts("") : 0))
#define Dor(a, b, c) for(int a = (b); a >= (c); -- a)
#define uDor(a, b, c) for(int a = (b); a > (c); -- a)
#define Pdor(a, b, c) for(int a = (b); a >= (c); -- a, puts(""))
#define Dorp(a, b, c) for(int a = (b); a >= (c); -- a, (a == (c - 1) ? puts("") : 0))
#define Por(a) printf("%s = %d\n", #a, a)
#define Rpor(a, b) printf("%s, %s = %d, %d\n", #a, #b, a, b)
#define Tor(a, b) printf("%s = %d : %d\n", #a, a, b)
#define Sbg puts("\nBegin")
#define Sed puts("End\n")
const int maxn = 2e5 + 5, mod = 998244353, G = 3, invG = 332748118;
int n, m, rev[maxn], lim, len;

void getrev(int x) {
    for(lim = 1, len = 0; lim <= x; lim <<= 1, ++ len);
    uFor(i, 0, lim) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
}

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

void NTT(int *A,int op = 1) {
    uFor(i, 0, lim) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1; mid < lim; mid <<= 1) {
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            int w = 1, wn = ksm(op == 1 ? G : invG, (mod - 1) / c);
            for(int k = 0; k < mid; ++ k, w = 1ll * w * wn % mod) {
                int x = A[j + k], y = 1ll * A[j + k + mid] * w % mod;
                A[j + k] = (x + y) % mod;
                A[j + k + mid] = (x - y + mod) % mod;
            }
        }
    }
    if(op != 1) {
        int z = ksm(lim, mod - 2);
        uFor(i, 0, lim) A[i] = 1ll * A[i] * z % mod;
    }
}

int fac[maxn], finv[maxn];
void init(int x) {
    fac[0] = 1;
    For(i, 1, x) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = ksm(fac[x], mod - 2);
    Dor(i, x - 1, 0) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
}

int C(int a,int b) {
    if(a < b || a < 0 || b < 0) return 0;
    return 1ll * fac[a] * finv[b] % mod * finv[a - b] % mod;
}

int a[maxn];

signed main() {
	int i, j, x;

	auto Calc = [&] () -> void{
        static int pw[maxn], pw1[maxn];
        memset(pw, 0, sizeof(pw)), memset(pw1, 0, sizeof(pw1));
        pw[0] = pw1[0] = 1;
        For(i, 1, n) pw[i] = 1ll * pw[i - 1] * x % mod;
        For(i, 1, n) pw1[i] = 1ll * pw1[i - 1] * (1 - x + mod) % mod;
        int ans(0);
        For(i, 0, n) {
            ans = (ans + 1ll * a[i] * C(n, i) % mod * pw[i] % mod * pw1[n - i]) % mod;
        }
        printf("%d\n", ans);
	};
    r1(n, m, x);
    For(i, 0, m) r1(a[i]);
    init(m);
    if(n <= m) return Calc(), 0;
    static int A[maxn], B[maxn];
    memset(A, 0, sizeof(A)), memset(B, 0, sizeof(B));
    For(i, 0, m) {
        A[i] = (i & 1 ? mod - finv[i] : finv[i]);
        B[i] = 1ll * a[i] * finv[i] % mod;
    }
    getrev(m << 1);
    NTT(A), NTT(B);
    uFor(i, 0, lim) A[i] = 1ll * A[i] * B[i] % mod;
//    uFor(i, 0, lim) Tor(i, A[i]);
    NTT(A, -1);
    int pw(1), ans(0);
    For(i, 0, m) {
        ans = (ans + 1ll * pw * A[i]) % mod;
        pw = 1ll * pw * x % mod * (n - i) % mod;
    }
    printf("%d\n", ans);
	return 0;
}
}

signed main() { return Legendgod::main(), 0; }//

```





