---
title: CF1380D Berserk And Fireball
date: 2021-09-30 20:07:23
tags:
  - 贪心
  - 构造
categories:
  - CF题解
---

<h3><center>CF1380D  Berserk And Fireball</center></h3>

> [CF1380D  Berserk And Fireball](https://codeforces.ml/problemset/problem/1380/D)

其实不能算一个构造题，主要难在代码实现。

考虑每一个区间，对于这个区间选择一个最优的方法删除即可。

显然我们考虑用当前区间最大的数去删除其他的数，之后再将其删除即可。

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

#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

int n, m, K, X, Y, flag;
int a[maxn], b[maxn];

int f(int l,int r) {
    int ln = r - l - 1;
    if(ln < 0) return 0;
    int res = (ln % K) * Y + (ln / K) * min(X, Y * K);
    if(*max_element(a + l + 1, a + r) > max(a[l], a[r])) {
        if(ln / K == 0) flag = 1;
        res = res + X - min(Y * K, X);
    }
    return res;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, k;
    r1(n, m, X, K, Y);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= m; ++ i) r1(b[i]);
    int res(0);
    for(i = 1, k = 1, j = 0; i <= m + 1; j = k, ++ i) {
        for(; !flag && a[k] != b[i]; ++ k)
            flag = (k > n) && (b[i]);
        res += f(j, k);
    }
    printf("%lld\n", flag ? -1 : res);
	return 0;
}

```




