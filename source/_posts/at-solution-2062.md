---
title: AT2062 [AGC005D] ~K Perm Counting 题解
date: 2021-10-11 16:25:43
tags:
  - 图论
  - 数论
  - 多项式
categories:
  - AT题解
---

<h3><center>AT2062 [AGC005D] ~K Perm Counting</center></h3>

> [AT2062 [AGC005D] ~K Perm Counting](https://www.luogu.com.cn/problem/AT2062)
>
> 一个有趣的做法。

---

发现合法的情况直接算是不好算的，我们考虑进行二项式反演，也就是钦定有多少个是不合法的。

考虑一个位置 $i$ 可以向 $i\pm k$ 连边。

我们不妨考虑左边是排列，右边是位置的二分图。

那么本质上钦定 $i$ 个不合法的就是对于这样的二分图满足其匹配是 $i - 1$。

对于匹配是 $i$，长度是 $j$ 的链，方案数就是 $\dbinom{i + j - 1}{i}$。

考虑总共链的条数是不超过 $2K$ 条。显然可能会出现两种链，一种长度是 $\lfloor\frac{n}{K} + 1\rfloor$ 和 $\lfloor\frac{n}{K}\rfloor$。

因为每一条链是无关的，我们之后直接将其变成生成函数的卷积即可。

设 $F(x) = \sum_{i = 0} ^ n \binom{\lfloor\frac{n}{K}\rfloor - i}{i} z^i$。

之后两个进行背包卷积即可，发现我们只需要使用 $n$ 项，所以我们直接进行转换后的点值快速幂即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
#define getchar gc
#endif // Fread

template <typename T>
void r1(T &x) {
	x = 0;
	char c(getchar());
	int f(1);
	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
	x *= f;
}

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

#ifdef Getmod
const int mod  = 924844033;
template <int mod>
struct typemod {
    int z;
    typemod(int a = 0) : z(a) {}
    inline int inc(int a,int b) const {return a += b - mod, a + ((a >> 31) & mod);}
    inline int dec(int a,int b) const {return a -= b, a + ((a >> 31) & mod);}
    inline int mul(int a,int b) const {return 1ll * a * b % mod;}
    typemod<mod> operator + (const typemod<mod> &x) const {return typemod(inc(z, x.z));}
    typemod<mod> operator - (const typemod<mod> &x) const {return typemod(dec(z, x.z));}
    typemod<mod> operator * (const typemod<mod> &x) const {return typemod(mul(z, x.z));}
    typemod<mod>& operator += (const typemod<mod> &x) {*this = *this + x; return *this;}
    typemod<mod>& operator -= (const typemod<mod> &x) {*this = *this - x; return *this;}
    typemod<mod>& operator *= (const typemod<mod> &x) {*this = *this * x; return *this;}
    int operator == (const typemod<mod> &x) const {return x.z == z;}
    int operator != (const typemod<mod> &x) const {return x.z != z;}
};
typedef typemod<mod> Tm;
#endif

//#define int long long
const int maxn = 4e5 + 5;
const int maxm = maxn << 1;

int n, K;

Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}

Tm fac[maxn], inv[maxn];

void init(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i)  fac[i] = fac[i - 1] * i;
    inv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) inv[i] = inv[i + 1] * (i + 1);
}

Tm C(int a,int b) {
    if(a < b) return 0;
    return fac[a] * inv[b] * inv[a - b];
}

Tm G = 5, invG = 332748118;

Tm A[maxn], B[maxn];

int rev[maxn], len, lim;

void getrev(int x) {
    invG = ksm(G, mod - 2);
    lim = 1, len = 0;
    while(lim <= x) lim <<= 1, ++ len;
    for(int i = 0; i < lim; ++ i) rev[i] = ( rev[i >> 1] >> 1 ) | ((i & 1) << (len - 1));
}

void NTT(Tm *A,int opt = 1) {
    for(int i = 0; i < lim; ++ i) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1; mid < lim; mid <<= 1) {
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            Tm w(1), wn(ksm(opt == 1 ? G : invG, (mod - 1) / (mid << 1)));
            for(int k = 0; k < mid; ++ k, w *= wn) {
                Tm x = A[j + k], y = A[j + k + mid] * w;
                A[j + k] = x + y;
                A[j + k + mid] = x - y;
            }
        }
    }
    if(opt != 1) {
        Tm z = ksm(lim, mod - 2);
        for(int i = 0; i < lim; ++ i) A[i] *= z;
    }
}

signed main() {
//    freopen("permutation.in", "r", stdin);
//    freopen("permutation.out", "w", stdout);
    int i, j;
    init(2e5);
    r1(n, K);

    int sumA = K - (n % K), sumB = n % K;
    for(i = 0; i <= n; ++ i) A[i] = C(n / K - i, i), B[i] = C(n / K + 1 - i, i);

    getrev(2 * n);

    NTT(A, 1), NTT(B, 1);
    for(i = 0; i < lim; ++ i) A[i] = ksm(A[i], sumA * 2) * ksm(B[i], sumB * 2);
    NTT(A, -1);

    Tm ans(0);
    Tm vis[2] = {1, mod - 1};
    for(i = 0; i <= n; ++ i) {
        ans += vis[i & 1] * A[i] * fac[n - i];
    }
    printf("%d\n", ans.z);
	return 0;
}
```


