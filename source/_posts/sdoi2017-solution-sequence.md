---
title: "[SDOI2017]序列计数"
date: 2021-09-23 13:57:54
tags: 
  - Dp
  - 矩阵
categories: 
  - 省选题解
---

<h3><center>P3702 [SDOI2017]序列计数</center></h3>

> [P3702 [SDOI2017]序列计数](https://www.luogu.com.cn/problem/P3702)

> 首先这个真的要骂一下自己，这个第一步显然就是正难则反。如果直接考虑进行 $\tt Dp$ 是否包含质数不是很方便我们可以直接使用容斥来做。

我们考虑正难则反进行计算，也就是计算总方案减去全部是合数的方案。

设 $dp(i, j, k)$ 表示考虑到第 $i$ 位，总和 $\mod p = j$ 是否有质数的方案。

之后考虑转移本质上就是枚举每一个数然后将之前的数加上这个数的贡献。

也就是 $dp(i, j) \to dp(i + 1, j + c)$ 。我们把这个 $j$ 当做上指标那么就是一个卷积的形式。

对于 $n$ 是 $10^9$ 级别我们直接进行快速幂即可，至于卷积可以直接暴力循环卷积。

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
const int mod  = 20170408;
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
const int maxn = 3e2 + 5;
const int maxm = 2e7 + 5;

int n, m, p;

int pr[maxm], vis[maxm], tot;
void init(int x) {
    for(int i = 2; i <= x; ++ i) {
        if(!vis[i]) pr[++ tot] = i;
        for(int j = 1, k; k = i * pr[j], k <= x && j <= tot; ++ j) {
            vis[k] = 1;
            if(!(i % pr[j])) break;
        }
    }
}

Tm f[maxn], g[maxn];
Tm ansf[maxn], ansg[maxn];

Tm res[maxn];

void mul(Tm *a,Tm *b) {
    for(int i = 0; i < p; ++ i) for(int j = 0; j < p; ++ j)
        res[i + j] += a[i] * b[j];
    for(int i = 0; i < p; ++ i) a[i] = res[i] + res[i + p], res[i] = res[i + p] = Tm(0);
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m, p);
    init(m);
    f[1] = g[1] = ansf[0] = ansg[0] = 1;
    Tm ans(0);
    for(i = 2; i <= m; ++ i) {
        f[i % p] += 1;
        if(vis[i]) g[i % p] += 1;
    }
    while(n) {
        if(n & 1) mul(ansf, f), mul(ansg, g);
        n >>= 1;
        mul(f, f), mul(g, g);
    }
//    printf("%d %d\n", ansf[0].z, ansg[0].z);
    ans = ansf[0] - ansg[0];
    printf("%d\n", ans.z);
	return 0;
}

```




