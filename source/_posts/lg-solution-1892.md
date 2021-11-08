---
title: Luogu1892 [BOI2003]团伙
date: 2021-09-17 07:23:29
tags:
  - 并查集
categories:
  - 洛谷题解
---
[P1892 [BOI2003]团伙](https://www.luogu.com.cn/problem/P1892)

> 说句实话这个题意真的有点 $\dots$，具体来说就是两个方面。

> - 我的敌人和朋友的敌人不一定是朋友。

> - 最终答案要求输出总共有几个团体。（来自某讨论区小朋友的错误）

----

直接扩展域并查集即可。

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

//#define int long long
const int maxn = 1e3 + 5;
const int maxm = maxn << 1;

int res(0), fa[maxn], en[maxn];

int getfa(int x) {
    return x == fa[x] ? x : fa[x] = getfa(fa[x]);
}

void merge(int u,int v) {
    u = getfa(u), v = getfa(v);
    if(u != v) fa[u] = v;
}

char s[10];

int n, m;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) fa[i] = i;
    for(i = 1; i <= m; ++ i) {
        int x, y;
        scanf("%s", s + 1), r1(x, y);
        if(s[1] == 'F') merge(x, y);
        else {
            if(!en[x]) en[x] = getfa(y);
            else merge(en[x], y);
            if(!en[y]) en[y] = getfa(x);
            else merge(en[y], x);
        }
    }

    for(i = 1; i <= n; ++ i) if(i == getfa(i)) ++ res;

    printf("%d\n", res);

	return 0;
}


```
