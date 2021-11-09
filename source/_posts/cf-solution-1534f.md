---
title: "CF1534F2 Falling Sand (Hard Version)"
date: 2021-08-17 21:27:51
tags:
  - 贪心
  - 图论
categories:
  - CF题解
---


### [CF1534F2](https://codeforces.com/problemset/problem/1534/F2)

发现这是一个 $4$ 联通问题。我们先考虑建图。

我们需要每一个序列都满足条件，那么我们可以选取最低的点然后考虑其被左右两边影响的区间，也就是左右两边任意掉落一个沙子即可将其掉落。

这个可以使用 $dfs$ 解决。然后我们就得到 $n$ 个满足条件的区间。

我们将题目变成了给定 $n$ 个区间，选择最少的点使得每一个区间至少有一个点。我们按照左端点为第一关键字，右端点为第二关键字排序。尽量选右边的点即可。

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

//#define int long long
const int maxn = 4e5 + 5;
const int maxm = maxn << 1;

int n, m;

int a[maxn];

#define id(i, j) ((i - 1) * m + j)

char s[maxn];

int L[maxn], R[maxn];

void dfs1(int x,int y,int c) {
    if(L[id(x, y)] != -1) return ;
    L[id(x, y)] = c;
    if(x < n) dfs1(x + 1, y, c);
    if(x > 1 && s[id(x - 1, y)] == '#') dfs1(x - 1, y, c);
    if(y > 1 && s[id(x, y - 1)] == '#') dfs1(x, y - 1, c);
    if(y < m && s[id(x, y + 1)] == '#') dfs1(x, y + 1, c);
}

void dfs2(int x,int y,int c) {
    if(R[id(x, y)] != -1) return ;
    R[id(x, y)] = c;
    if(x < n) dfs2(x + 1, y, c);
    if(x > 1 && s[id(x - 1, y)] == '#') dfs2(x - 1, y, c);
    if(y > 1 && s[id(x, y - 1)] == '#') dfs2(x, y - 1, c);
    if(y < m && s[id(x, y + 1)] == '#') dfs2(x, y + 1, c);
}

int f[maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    memset(L, -1, sizeof(L)), memset(R, -1, sizeof(R));
    r1(n, m);
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= m; ++ j) {
            cin >> s[id(i, j)];
        }
    }

    for(i = 1; i <= m; ++ i) r1(a[i]);

    for(j = 1; j <= m; ++ j) for(i = 1; i <= n; ++ i) if(s[id(i, j)] == '#') dfs1(i, j, j);
    for(j = m; j >= 1; -- j) for(i = 1; i <= n; ++ i) if(s[id(i, j)] == '#') dfs2(i, j, j);

//    puts("ZZZ");

    for(i = 1; i <= m + 1; ++ i) f[i] = m + 2;
    for(j = 1; j <= m; ++ j) if(a[j]) {
        for(i = n; i; -- i) if(s[id(i, j)] == '#') {
            -- a[j];
            if(a[j] == 0) {
                f[L[id(i, j)]] = min(f[L[id(i, j)]], R[id(i, j)] + 1);
            }
        }
    }

//    puts("ZZZ");

    int ans(0), pos(1);
    for(i = m; i; -- i) f[i] = min(f[i], f[i + 1]);
    while(pos <= m + 1) ++ ans, pos = f[pos];
    printf("%d\n", ans - 1);

	return 0;
}
```




