---
title: "「CEOI2019」游乐园"
date: 2021-08-04 12:20:41
tags:
  - 反演
  - 图论
  - 数论
  - Dp
categories:
  - 省选题解
---

### [「CEOI2019」游乐园 题解](https://loj.ac/p/3165)
首先看到这个是一个数图的题目。
这里有一个转化，考虑翻转边本质上就是对这个图进行一个定向，容易想到翻转的边和自己定向的 $DAG$ 是一一对应的。

所以我们考虑给定一个无向图，对于每一条边进行定向，求定向后是 $DAG$ 的图的贡献。

然后我们考虑对于一个 $DAG$ 我们把所有的边都翻转了肯定也还是一个 $DAG$。

所以对于每一个合法的方案都有一个可以和其进行配对的图，其贡献之和是 $m$。所以每一张图的贡献可以当做 $\dfrac{m}{2}$ 来计算。

发现 $n \le 18$ 使用状压 $Dp$。设 $f(S)$ 表示只选用了 $S$ 中的点，构成合法图的方案数，之后考虑转移是通过枚举一个集合进行转移。对于 $S$ 通过 $S'$ 转移到 $T$，我们必须要保证 $S'$ 内部是没有边的，才可以让我们进行定向。然后对于 $S'$ 其贡献又会被计算多次，也就是对于其每一个子集其都会被计算一次贡献，当然转移肯定不能使空集，所以总共被计算的次数是 $2^{|S|} - 1$。直接进行容斥就好了。

至于容斥系数，我们直接考虑只有一个点转移就是 $1$，所以容斥系数就是 $(-1)^{|S| + 1}$

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
 #define Getmod

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

const int mod  = 998244353;
#ifdef Getmod
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

// #define int long long
const int maxn = (1 << 18) + 5;
int n, m;
Tm f[maxn];
int u[maxn], v[maxn], fl[maxn], cnt[maxn];;

Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}

#define Online
signed main() {
#ifndef Online
    freopen("S.in", "r", stdin);
    freopen("S.out", "w", stdout);
#endif
    int i, j;
    Tm vis[2] = {1, mod - 1};
    r1(n, m);
    int z = (1 << n);
    for(i = 1; i < z; ++ i) cnt[i] = cnt[i >> 1] + (i & 1);
    f[0] = 1;
    for(i = 1; i <= m; ++ i) {
        r1(u[i], v[i]), -- u[i], -- v[i];
    }
    for(int S = 0; S < z; ++ S) {
        for(i = 1; i <= m; ++ i) if((S >> u[i]) & 1 && (S >> v[i]) & 1) {
            fl[S] = 1; break;
        }
    }
    for(int S = 0; S < z; ++ S) {
        for(int S1 = S; S1; S1 = (S1 - 1) & S) if(fl[S1] == 0) {
            f[S] += f[S ^ S1] * vis[(cnt[S1] + 1) & 1];
        }
    }
    Tm inv2 = ksm(2, mod - 2);
    Tm ans = inv2 * f[z - 1] * m;
    printf("%d\n", ans.z);
	return 0;
}

```



