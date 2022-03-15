---
title: 后缀自动机 SAM 做题记录 
date:  2021-06-25 09:01:23 
tags:
  - 后缀自动机
categories:
  - 算法
---

[统计本质不同的子串个数](https://hihocoder.com/problemset/problem/1445)

> 小Hi平时的一大兴趣爱好就是演奏钢琴。我们知道一个音乐旋律被表示为一段数构成的数列。

> 现在小Hi想知道一部作品中出现了多少不同的旋律

> $O(n)$。

```cpp
#include <cstring>
#include <cstdio>
#include <algorithm>
#include <iostream>
/*
* @ legendgod
* 是啊……你就是那只鬼了……所以被你碰到以后，就轮到我变成鬼了
*/
using namespace std;

template <typename T>
void r1(T &x) {
    x = 0;
    char c(getchar());
    int f(1);
    for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
    for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
    x *= f;
}

//#define int long long
const int maxn = 1000000 + 5;
const int maxm = maxn << 1;

struct Node {
    int c[26], len, fa;
    Node(void) {
        memset(c, 0, sizeof(c)), len = fa = 0;
    }
}d[maxn << 1];
int las(1), tot(1);
void Insert(int c) {
    int p = las;
    int np = las = ++ tot;
    d[np].len = d[p].len + 1;
    for(; p && !d[p].c[c]; p = d[p].fa) d[p].c[c] = np;
    if(!p) d[np].fa = 1;
    else {
        int q = d[p].c[c];
        if(d[q].len == d[p].len + 1) d[np].fa = q;
        else {
            int nq = ++ tot; d[nq] = d[q];
            d[nq].len = d[p].len + 1;
            d[q].fa = d[np].fa = nq;
            for(; p && (d[p].c[c] == q); p = d[p].fa) d[p].c[c] = nq;
        }
    }
}

char c[maxn];
int n;
long long dp[maxn << 1];

void dfs(int p) {
    dp[p] = 1;
    for(int i = 0; i < 26; ++ i) if(d[p].c[i]) {
//        if(!dp[d[p].c[i]])
        dfs(d[p].c[i]);
        dp[p] += dp[d[p].c[i]];
    }
}

signed main() {
//    freopen(".in", "r", stdin);
//    freopen(".out", "w", stdout);
    int i, j;
    scanf("%s", c + 1);
    n = strlen(c + 1);
    for(i = 1; i <= n; ++ i) Insert(c[i] - 'a');
    dfs(1);
    printf("%lld\n", dp[1] - 1);
    return 0;
}
```

[计算任意子串出现次数](https://hihocoder.com/problemset/problem/1449)

> 小Hi平时的一大兴趣爱好就是演奏钢琴。我们知道一个音乐旋律被表示为一段数构成的数列。

> 现在小Hi想知道一部作品中所有长度为K的旋律中出现次数最多的旋律的出现次数。但是K不是固定的，小Hi想知道对于所有的K的答案

> $O(n)$

```cpp
#include <bits/stdc++.h>
/*
* @ legendgod
* 是啊……你就是那只鬼了……#include <bits/stdc++.h>
*/
using namespace std;

//#define Fread
// #define Getmod

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

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

struct Node {
    int c[26], len, fa;
    Node(void) {
        memset(c, 0, sizeof(c)), len = fa = 0;
    }
}d[maxn << 1];

int las(1), tot(1);
vector<int> vc[maxn << 1];
int dp[maxn], tag[maxn];

void Insert(int c) {
    int p = las, np = las = ++ tot;
    d[np].len = d[p].len + 1;
    dp[np] = 1;
    for(; p && !d[p].c[c]; p = d[p].fa) d[p].c[c] = np;
    if(!p) d[np].fa = 1;
    else {
        int q = d[p].c[c];
        if(d[q].len == d[p].len + 1) d[np].fa = q;
        else {
            int nq = ++ tot;
            d[nq] = d[q];
            d[nq].len = d[p].len + 1;
            d[q].fa = d[np].fa = nq;
            for(; p && d[p].c[c] == q; p = d[p].fa) d[p].c[c] = nq;
        }
    }
}

char c[maxn];
int n, deg[maxn];

void build() {
    for(int i = 2; i <= tot; ++ i)
        vc[i].push_back(d[i].fa), ++ deg[d[i].fa];
}


void Topu() {
    static queue<int> q;
    int i;
    build();
    while(!q.empty()) q.pop();
    for(i = 1; i <= tot; ++ i) if(!deg[i]) q.push(i);
    while(!q.empty()) {
        int u = q.front(); q.pop();
        for(auto v : vc[u]) {
            dp[v] += dp[u];
            if(!(-- deg[v])) q.push(v);
        }
    }
    for(i = 2; i <= tot; ++ i) tag[d[i].len] = max(tag[d[i].len], dp[i]);
    for(i = n; i; -- i) tag[i] = max(tag[i], tag[i + 1]);
}

signed main() {
//    freopen("data.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    scanf("%s", c + 1);
    n = strlen(c + 1);
    for(i = 1; i <= n; ++ i) Insert(c[i] - 'a');
    Topu();
    for(i = 1; i <= n; ++ i) printf("%d\n", tag[i]);
    return 0;
}
```

[统计所有本质不同子串的权值和](https://hihocoder.com/problemset/problem/1457)

> 小Hi平时的一大兴趣爱好就是演奏钢琴。我们知道一段音乐旋律可以被表示为一段数构成的数列。

> 神奇的是小 $Hi$ 发现了一部名字叫《十进制进行曲大全》的作品集，顾名思义，这部作品集里有许多作品，但是所有的作品有一个共同特征：只用了十个音符，所有的音符都表示成 $0-9$ 的数字。

> 现在小Hi想知道这部作品中所有不同的旋律的“和”（也就是把串看成数字，在十进制下的求和，允许有前导 $0$ ）。答案有可能很大，我们需要对（$10^9 + 7$)取摸。

```cpp
#include <bits/stdc++.h>
/*
* @ legendgod
* 是啊……你就是那只鬼了……#include <bits/stdc++.h>
*/
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
const int maxn = 2e6 + 5;
const int maxm = maxn << 1;
char c[maxn];
int n;

struct Node {
    int c[22], len, fa;
    Node(void) {
        memset(c, 0, sizeof(c));
        len = fa = 0;
    }
}d[maxn << 1];
int las(1), tot(1);
void Insert(int c) {
    int p = las, np = las = ++ tot;
    d[np].len = d[p].len + 1;
    for(; p && !d[p].c[c]; p = d[p].fa) d[p].c[c] = np;
    if(!p) d[np].fa = 1;
    else {
        int q = d[p].c[c];
        if(d[q].len == d[p].len + 1) d[np].fa = q;
        else {
            int nq = ++ tot; d[nq] = d[q];
            d[nq].len = d[p].len + 1;
            d[q].fa = d[np].fa = nq;
            for(; p && d[p].c[c] == q; p = d[p].fa) d[p].c[c] = nq;
        }
    }
}

const int N = 10;

Tm val[maxn], tt[maxn];
int deg[maxn];
Tm ans[maxn], res(0);
int mx(0);

void build() {
    static queue<int> q;
    while(!q.empty()) q.pop();
    int i, j;
    tt[1] = 1;
    q.push(1);
    for(i = 1; i <= tot; ++ i) {
        for(j = 0; j <= N; ++ j) if(d[i].c[j]) ++ deg[d[i].c[j]];
    }
    while(!q.empty()) {
        int u = q.front(); q.pop();
        for(i = 0; i <= N; ++ i) {
            int v = d[u].c[i];
            if(!v)  continue;
            if(i != 10) {
                tt[v] += tt[u];
                val[v] += val[u] * Tm(10) + Tm(i) * tt[u];
            }
            if(!(-- deg[v])) q.push(v);
        }
    }
    for(i = 1; i <= tot; ++ i) res += val[i];
}

signed main() {
    freopen("data.in", "r", stdin);
    freopen("S.out", "w", stdout);
    int i, j, T;
    r1(T);
    for(int _ = 1; _ <= T; ++ _) {
        scanf("%s", c + 1);
        n = strlen(c + 1);
        mx = max(mx, n);
        for(i = 1; i <= n; ++ i) Insert(c[i] - '0');
        if(_ != T) Insert(10);
    }
    build();
//    for(i = 1; i <= mx; ++ i) res += ans[i];
    printf("%d\n", res.z);
    return 0;
}
```

[P3181 [HAOI2016]找相同字符](https://www.luogu.com.cn/problem/P3181)

```cpp
#include <bits/stdc++.h>
/*
* @ legendgod
* 是啊……你就是那只鬼了……#include <bits/stdc++.h>
*/
using namespace std;

//#define Fread
// #define Getmod

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

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;
int c1[maxn << 1], t1[maxn << 1];
class SAMA {
public:
    int n;
    class Node {
        public:
        int fa, len, c[26];
    }d[maxn << 1];
    int tot = 1, las = 1;
    long long val[maxn << 1];
    void Insert(int c) {
        int p = las, np = las = ++ tot;
        val[np] = 1;
        d[np].len = d[p].len + 1;
        for(; p && !d[p].c[c]; p = d[p].fa) d[p].c[c] = np;
        if(!p) d[np].fa = 1;
        else {
            int q = d[p].c[c];
            if(d[q].len == d[p].len + 1) d[np].fa = q;
            else {
                int nq = ++ tot; d[nq] = d[q];
                d[nq].len = d[p].len + 1;
                d[q].fa = d[np].fa = nq;
                for(; p && d[p].c[c] == q; p = d[p].fa) d[p].c[c] = nq;
            }
        }
    }
    void build() {
        memset(c1, 0, sizeof(c1)), memset(t1, 0, sizeof(t1));
        int i;
        for(i = 1; i <= tot; ++ i) c1[d[i].len] ++;
        for(i = 1; i <= n; ++ i) c1[i] += c1[i - 1];
        for(i = 1; i <= tot; ++ i) t1[c1[d[i].len] --] = i;
        for(i = tot; i; -- i) val[d[t1[i]].fa] += val[t1[i]];
    }
}S1, S2;
char c[maxn];
int n1, n2;

long long ans;

void dfs(int x,int y) {
    for(int i = 0; i < 26; ++ i) {
        if(S1.d[x].c[i] && S2.d[y].c[i])
            dfs(S1.d[x].c[i], S2.d[y].c[i]);
    }
    if(x != 1 && y != 1) ans += S1.val[x] * S2.val[y];
}

signed main() {
//    freopen(".in", "r", stdin);
//    freopen(".out", "w", stdout);
    int i, j;
    scanf("%s", c + 1);
    n1 = strlen(c + 1);
    for(i = 1; i <= n1; ++ i) S1.Insert(c[i] - 'a');
    scanf("%s", c + 1);
    n2 = strlen(c + 1);
    for(i = 1; i <= n2; ++ i) S2.Insert(c[i] - 'a');
    S1.n = n1, S2.n = n2;
    S1.build(), S2.build();
    dfs(1, 1);
    printf("%lld\n", ans);
    return 0;
}
```

[你的名字](https://www.luogu.com.cn/blog/legendgod/ni-di-ming-zi-ti-xie)
