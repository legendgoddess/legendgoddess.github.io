---
title: CF1458C Latin Square
date: 2021-09-21 21:04:07
tags: 
  - 构造
  - 贪心
categories: 
  - CF题解
---

<h3><center>CF1458C Latin Square</center></h3>

> [CF1458C Latin Square](https://codeforces.com/problemset/problem/1458/C)

这里说一下逆排序就是如果说原来位置 $i$ 的数是 $p_i$ 那么现在位置 $p_i$ 的数就是 $i$。

直接变成三元组 $(i, j, a_{i, j})$ 也就是所有有关的信息。

之后直接记录并且修改即可，复杂度是 $O(n^2 + m)$ 的。

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

int pos[4], n, m, sum[4];
int ans[maxn][maxn];
int a[maxn * maxn][3];

char s[maxn * 100];

int id(int x,int y) {
    return (x - 1) * n + y;
}

void solve() {
    int i, j;
    r1(n, m);
    for(i = 0; i <= 2; ++ i) pos[i] = i, sum[i] = 0;
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= n; ++ j) {
            r1(a[id(i, j)][2]);
            a[id(i, j)][0] = i, a[id(i, j)][1] = j;
        }
    }
    scanf("%s", s + 1);
    for(i = 1; i <= m; ++ i) {
        char c = s[i];
        if(c == 'U') -- sum[pos[0]];
        if(c == 'D') ++ sum[pos[0]];
        if(c == 'L') -- sum[pos[1]];
        if(c == 'R') ++ sum[pos[1]];
        if(c == 'I') swap(pos[1], pos[2]);
        if(c == 'C') swap(pos[0], pos[2]);
    }
    for(i = 1; i <= n * n; ++ i) {
        for(j = 0; j <= 2; ++ j) a[i][j] = (a[i][j] + sum[j] - 1 + m * n) % n + 1;
        ans[a[i][pos[0]]][a[i][pos[1]]] = a[i][pos[2]];
    }
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= n; ++ j) printf("%d ", ans[i][j]);
        puts("");
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, T;
    r1(T);
    while(T --) solve();
	return 0;
}

```




