---
title: AT2000 [AGC002F] Leftmost Ball 题解
date: 2021-10-11 14:54:26
tags:
  - 数论
  - Dp
categories:
  - AT题解
---

<h3><center>AT2000 [AGC002F] Leftmost Ball</center></h3>

> [AT2000 [AGC002F] Leftmost Ball](https://www.luogu.com.cn/problem/AT2000)

> 感觉转移还是挺难的。$\color{white} Orz\ AK\_STAR$

---

我们不妨考虑随便染色，对于一种合法的染色肯定是对于任意前缀和白色的数量都大于等于颜色的数量。

那么我们不妨考虑通过白色来计数，我们不妨钦定每次填一种颜色的时候直接匹配最靠左没有匹配过的白色点。

设 $f(i, j)$ 表示填了 $i$ 个白色的球，填了 $j$ 种颜色。

我们转移考虑枚举当前位置填的是哪种颜色的球。

- 白色球，直接从 $f(i - 1, j)$ 进行转移。

- 颜色球，首先需要知道是哪个颜色的球 $n - j + 1$ 还要知道有哪些位置是可以填的 $n \times K - i - 1 - (j - 1) \times (K - 1)$，之后我们发现已经钦定了一个白球作为颜色的开始，而且当前位置又必须填一个球，所以剩下 $K - 2$ 个球来填。

  那么转移系数就是 $(n - j + 1) \times \dbinom{n\times K - i - 1  - (j - 1) \times (K - 1)}{K - 2} \times f(i, j - 1)$。

---

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
const int mod  = 1e9 + 7;
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
const int maxn = 2e3 + 5;
const int maxm = maxn << 1;

Tm fac[maxn * maxn], inv[maxn * maxn];
Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}
void init(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = fac[i - 1] * i;
    inv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) inv[i] = inv[i + 1] * (i + 1);
}

int n, K;
Tm C(int a,int b) {
    return a < b ? 0 : fac[a] * inv[b] * inv[a - b];
}
Tm f[maxn][maxn];

signed main() {
    int i, j;
    r1(n, K);
    init(n * K);
    if(K == 1) return puts("1"), 0;
//    printf("%d\n", C(2, 2).z);
    f[0][0] = 1;
    for(i = 1; i <= n; ++ i) {
        for(j = 0; j <= i; ++ j) {
            f[i][j] = f[i - 1][j];
            if(j) f[i][j] += f[i][j - 1] * (n - j + 1) * C(n * K - i - 1 - (j - 1) * (K - 1), K - 2);
        }
    }
    printf("%d\n", f[n][n].z);
    return 0;
}
```








