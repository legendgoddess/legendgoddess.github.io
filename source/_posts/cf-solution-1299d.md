---
title: CF1299D Around the World
date: 2021-09-02 09:43:08 
tags:
  - 线性基
  - Dp
categories:
  - CF题解
---

### [CF1299D Around the World](https://www.luogu.com.cn/problem/CF1299D)

就是是否能有为 $0$ 的路径直接会想到线性基，也就是里面的环是可以走或者不走的。

我们写一手暴力发现大小为 $5$ 的线性基的个数不会很多，那么我们可以考虑对于每个联通块存一个线性基。

如果说联通块内部已经有环线性相关那么肯定是不能连边的。

如果线性无关那么考虑题目给出的性质，也就是和 $1$ 相连的环大小最多为 $3$。那么我们对于一个联通块最多有 $2$ 个点和 $1$ 相连。我们进行分类讨论有 $2$ 条边相连的情况。

- 断掉两条边也就是直接加入即可。
- 断掉一条边也是同理，枚举一下断掉那条边。
- 不断边，也就是意味着多一个 $3$ 元环我们需要将其加入并且判断线性关系。

不妨设 $dp(i, j)$ 表示考虑到底 $i$ 个联通块，之前的联通块的连接关系构成的线性基为 $j$。

设当前联通块的线性基是 $B$。

我们所有的情况都可以不加入，所以 $dp(i - 1, j) \to dp(i, j)$。

只有一条边 $dp(i - 1, j) \to dp(i, j \cup B)$。

如果有两条边 
$$
dp(i - 1, j) \times 2 \to dp(i, j \cup B) \\
dp(i - 1, j) \to dp(i, j \cup B \cup w)
$$
前面是判断断那条边，另外是考虑多加入一个 $3$ 元环的贡献。

------

我们只需要对于每个联通块维护和 $1$ 相连的三元环异或值，自己内部的线性基即可。

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
const int maxm = 374 + 5;
const int maxK = 31744 + 5;

int head[maxn], cnt;
struct Edge {
    int to, next, w;
}edg[maxn << 1];

void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) { v, head[u], w }, head[u] = cnt;
}

const int N = 4;
struct Seg {
    int a[5]; Seg(void) { memset(a, 0, sizeof(a)); }
    bool Insert(int x) {
        for(int i = N; ~ i; -- i) if((x >> i) & 1) {
            if(a[i]) x ^= a[i];
            else {
                a[i] = x;
                for(int j = i - 1; ~ j; -- j) if((a[i] >> j) & 1) a[i] ^= a[j];
                for(int j = N; j > i; -- j) if((a[j] >> i) & 1) a[j] ^= a[i];
                return true;
            }
        }
        return false;
    }
    int Hash() {
        return (a[0] | (a[1] << 1) | (a[2] << 3) | (a[3] << 6) | (a[4] << 10));
    }
}b[maxm], c[maxn];
int tr[maxm][maxm];
int id[maxK], num(0);

//int Mx(0);

void dfs(Seg p) {
    int hs = p.Hash(); if(id[hs]) return ;
    id[hs] = ++ num, b[num] = p;
//    Mx = max(Mx, hs);
    for(int i = 1; i <= 31; ++ i) {
        Seg np = p;
        if(np.Insert(i)) dfs(np);
    }
}

void init() {
    dfs(Seg());
//    printf("Mx = %d\n", Mx);
//    puts("ZZZ");
//    printf("num = %d\n", num);
    for(int i = 1; i <= num; ++ i) {
        for(int j = 1; j <= num; ++ j) {
            Seg tmp = b[i]; bool flag(1);
            for(int k = 0; k <= 4; ++ k) if(b[j].a[k]) flag &= tmp.Insert(b[j].a[k]);
            if(flag) tr[i][j] = id[tmp.Hash()];
        }
    }
}

int n, m;
bool pd[maxn];
int dis[maxn], Siz(0), st[maxn], Ec[maxn];
int bel[maxn], dfn[maxn], dfntot(0);
bool sed[maxn];
void dfs1(int p,int pre) {
    dfn[p] = ++ dfntot, bel[p] = Siz;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        if(to == 1 || to == pre) continue;
        if(!bel[to])  dis[to] = edg[i].w ^ dis[p], dfs1(to, p);
        else if(dfn[p] > dfn[to]) pd[Siz] &= c[Siz].Insert(dis[p] ^ dis[to] ^ edg[i].w);
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m); init();
    for(i = 1; i <= m; ++ i) {
        int u, v, w;
        r1(u, v, w), add(u, v, w), add(v, u, w);
    }
    for(i = head[1];i;i = edg[i].next) {
        int to = edg[i].to, w = edg[i].w;
        if(!bel[to]) {
            pd[++ Siz] = 1, Ec[Siz] = w, st[Siz] = to;
            dfs1(to, 0);
        }
        else {
            for(int j = head[to]; j; j = edg[j].next) {
                int to = edg[j].to, w1 = edg[j].w;
                if(st[bel[to]] == to) {
                    sed[bel[to]] = 1, Ec[bel[to]] ^= w ^ w1;
                }
            }
        }
    }

    vector<vector<Tm> > dp(Siz + 2, vector<Tm>(num + 2, 0) );
    dp[0][id[0]] = 1;
    for(i = 1; i <= Siz; ++ i) {
        for(j = 1; j <= num; ++ j) dp[i][j] = dp[i - 1][j];
        if(!pd[i]) continue;
        if(!sed[i]) {
            int hs = id[c[i].Hash()];
            for(j = 1; j <= num; ++ j) if(tr[j][hs])
                dp[i][tr[j][hs]] += dp[i - 1][j];
        }
        else {
            int hs = id[c[i].Hash()];
            bool flag = c[i].Insert(Ec[i]);
            int hs1 = id[c[i].Hash()];
            for(j = 1; j <= num; ++ j) {
                if(tr[j][hs]) dp[i][tr[j][hs]] += dp[i - 1][j] * 2;
                if(flag && tr[j][hs1]) dp[i][tr[j][hs1]] += dp[i - 1][j];
            }
        }
    }
    Tm ans(0);
    for(i = 1; i <= num; ++ i) ans += dp[Siz][i];
    printf("%d\n", ans.z);
	return 0;
}
```




