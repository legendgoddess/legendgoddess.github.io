---
title: POJ3146 Interesting Yang Hui Triangle
date: 2021-08-25 08:33:32 
tags:
  - 数论
categories:
  - POJ题解
---

### [poj3146](http://poj.org/problem?id=3146)

**题目大意：**

给定一个 $n, p$ 求 $\binom{n}{0 \dots n}$ 有多少数不能被 $p$ 整除。

我们考虑 $Lucas$ 定理。

$\binom{n}{m} = \binom{n / p}{m/ p} \times \binom{n \mod p}{m \mod p}$。

也就是将 $n$ 分解成 $p$ 进制。如果上面的大于下面的就是有值的。

所以直接进行分解之后乘法原理即可。

```cpp
#include <iostream>
#include <cstdio>
using namespace std;

//#define Fread
//#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
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

#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;
const int mod = 1e4;

int p, n;
int Work() {
    int sum(1);
    while(n) {
        int tmp = n % p; n /= p;
        ++ tmp;
        sum = sum * tmp % mod;
    }
    return sum;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, Case(0);
    while(scanf("%lld%lld", &p, &n) != EOF) {
        if(p == 0 && n == 0) return 0;
        ++ Case;
        printf("Case %lld: %04lld\n", Case, Work());
    }
	return 0;
}
```




