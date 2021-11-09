---
title: ABC212G Power Pair
date: 2021-08-21 15:51:56
tags:
  - 数论
  - 反演
categories:
  - AT题解
---

### [ABC212G **Power Pair**](https://atcoder.jp/contests/abc212/tasks/abc212_g)

给你一个质数 $P$。求点对 $(x, y), x, y \in [0, P - 1]$ 的点对数量。

其中 $x^k \equiv y \pmod P$。

可以想到如果能把指数拿下来会就变成一个乘积的形式，那么能将指数取下来的情况也就是底数相同。题目中给定的是一个素数 $P$。这也就是意味着其缩系的大小就是 $P - 1$。然后质数的原根也就是 $P - 1$。能表示题目中的任意数。

那么我们可以转换到 $g^{ka} \equiv g^b \pmod P$ 我们就可以把指数取下来。得到 $ka \equiv b \pmod {P - 1}$。我们把形式转换一下得到 $ka - y(P - 1) = b$。

根据 $\tt Lucas$ 定理这个方程有解的条件就是 $b | \gcd(a, P - 1)$。

> 这边可以将 $k$ 当成 $x$ 会更加显然。

那么 $b$ 的数量就是 $\frac{P - 1}{\gcd(a, P - 1)}$。

做法 $1$：

那么可以得到柿子 $\sum_{i = 1} ^ {P - 1} \frac{P -1} {i} \times \varphi(\frac{P - 1}i)$。

我们考虑原来的柿子是这样的 $\sum_{i = 1} ^ {P- 1} \frac{P - 1}{\gcd(i, P - 1)}$。我们考虑枚举公约数，考虑被计算了几次。

$\sum_{i = 1} ^ {P - 1} \frac{P - 1}{i} \times |S|$,其中 $\forall a \in S, i = \gcd(a, P -1)$ 那么也意味着 $1 = \gcd(\frac{a}{i}, \frac{P -1}{i})$  也就是求和 $\frac{P - 1}{i}$ 互质的数的个数，就是 $\varphi(\frac{P - 1}i)$。

做法 $2$：

考虑进行反演。
$$
\begin{aligned}
&\sum_{i = 1} ^ {P - 1} \frac{P - 1}{\gcd(i, P - 1)} \\ =& (P - 1) \sum_{i = 1} ^ {P - 1} \sum_{d = 1} ^ {P - 1} \frac{1}{d} [\gcd(\frac{i}{d}, \frac{P - 1}{d}) = 1]\\
= &\sum_{d | (P - 1)} ^{P - 1} \frac{1}{d}\sum_{d | i} ^{P - 1} \varphi(\frac{P - 1}{d}) \\
= &\sum_{d  | (P - 1)} ^ {P - 1} \frac{1}{d} \lfloor\frac{P -1}{d}\rfloor \times \varphi(\frac{P - 1}{d}) \\
\end{aligned}
$$
这边同样可以得到答案。


```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
//#define Getmod

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

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;
int P;
vector<int> s;
const int mod = 998244353;
int getphi(int x) {
    int res(x);
    for(int i = 2; i * i <= x; ++ i) if(x % i == 0) {
        res = res / i * (i - 1);
        while(x % i == 0) x /= i;
    }
    if(x > 1) res = res / x * (x - 1) ;
    return res % mod;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i;
    r1(P);
    for(i = 1; i * i <= P - 1; ++ i) if((P - 1) % i == 0) {
        s.push_back(i);
        if(i * i != (P - 1)) s.push_back((P - 1) / i);
    }
    int ans(0);
    for(int v : s) {
        ans += (P - 1) / v % mod * getphi((P - 1) / v) % mod;
        ans %= mod;
    }
    ans = (ans + 1) % mod;
    printf("%lld\n", ans);
	return 0;
}
```


