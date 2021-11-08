---
title: [PKUWC2018]Minimax
date: 2021-10-08 09:10:48
tags:
  - Dp
  - 线段树
categories:
  - WC题解
---


### [P5298 [PKUWC2018]Minimax](https://www.luogu.com.cn/problem/P5298)

> $2021.10.8$ 重新自己写出这题。

> 其实不要想太复杂就好了，别忘了看题面。

> 先给一个提示吧，为什么这题的 $Dp$ 不用融斥掉中间的部分，题目已经说出来了：**没有**重复权值的叶子。

首先考虑对于每一个节点的值最多只有 n 个，所以肯定需要离散化。
之后考虑实际上每一个节点的值都是从其子树的叶子节点中转移过来的。

所以考虑树形 Dp
> 设 $f[i][j]$ 表示在树上点 $i$ 其值为 $j$ 的概率。

> 当然是离散化之后的值。

我们考虑转移，根据题意是取 $\min, \max$ 来转移的，所以我们考虑左右两个儿子的大小关系。具体来说就是如果让左边的儿子产生了贡献，右边的儿子的值就一定比左边儿子小 ( 如果是取 $\min$ 的话 )。

就可以写出方程

这里设左边的儿子为 l 右边的儿子为 r

$$
\begin{aligned}
f[i][j] &= f[l][j] \times (p_i \times \sum_{k = 1} ^ {j - 1} f[r][k] + (1 - p_i) \times \sum_{k = j} ^ m f[r][k])\\
 &+ f[r][j] \times (p_i \times \sum_{k = 1} ^ {j - 1} f[l][k] + (1 - p_i) \times \sum_{k = j} ^ m f[l][k])
 \end{aligned}
$$

发现是一个前缀和，而且前缀和内之和一边的子树有关，所以考虑线段树合并。

这里具体说一下怎么合并。

可以考虑将左右两个儿子的线段树先合并到其中一个，再让根节点取代它。

```cpp
rt[p] = T.merge(rt[son[p][0]], rt[son[p][1]],1, mx, 0, 0, v[p]);
```

具体合并的时候，本来是应该从下向上，但是线段树是从上到下合并的，所以对于一个关于 j 的区间 $[l,r]$。我们必须在将其分成两边的时候，也就是变成 $[l,mid], [mid + 1,r]$ 去递归的时候，加上另一边的贡献。

要怎么加呢？根据方程，发现如果递归左边那就将其右边的部分加上。

> 如果递归 l 的 $[l, mid]$ 区间，那么就要加上 $(1 - p_i) \times \sum_{k = j} ^ m f[r][k]$

> 虽然方程是 $\sum_{k = 1} ^ {j - 1}$ 实际上到 j 是没有影响的，可以理解成将其分成两边，如果左边产生的贡献分两部分，一部分是取 $\min$ 另外是取 $\max$ 如果递归左边，那么除了都在左边产生的贡献，还有右边的贡献。

注意最后统计答案的时候一定要将所有标记都 pushdown。

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

//#define int long long
const int maxn = 3e5 + 5;
const int maxm = maxn << 1;
const int mod = 998244353;
int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}
const int Inv = ksm(10000, mod - 2);
int n;
vector<int> vc[maxn];
int val[maxn], p[maxn];
int c[maxn], ed(0), len;
int ansp[maxn];

int t[maxn * 20], tot(0);
int lsn[maxn * 20], rsn[maxn * 20], tag[maxn * 20];

struct Seg {
    #define ls lsn[p]
    #define rs rsn[p]
    #define mid ((l + r) >> 1)

    void pushup(int p) {
        t[p] = (t[ls] + t[rs]) % mod;
    }

    void Add(int p,int c) {
        if(p == 0) return ;
        t[p] = 1ll * t[p] * c % mod;
        tag[p] = 1ll * tag[p] * c % mod;
    }

    void pushdown(int p) {
        if(tag[p] == 1)  return ;
        Add(ls, tag[p]), Add(rs, tag[p]);
        tag[p] = 1;
    }

    void Insert(int &p,int l,int r,int pos,int c) {
        if(!p) p = ++ tot, tag[p] = 1;
        if(l == r) return t[p] = c, void();
        if(pos <= mid) Insert(ls, l, mid, pos, c);
        else Insert(rs, mid + 1, r, pos, c);
        pushup(p);
    }

    int merge(int u,int v,int l,int r,int mulu,int mulv,const int &P) {
//        printf("%d %d, (%d, %d), %d %d\n", u, v, l, r, mulu, mulv);
        if(!u) return Add(v, mulv), v;
        if(!v) return Add(u, mulu), u;
        pushdown(u), pushdown(v);
        int vul = t[lsn[u]], vur = t[rsn[u]], vvl = t[lsn[v]], vvr = t[rsn[v]];
        const int P1 = (1 - P + mod) % mod;
        lsn[u] = merge(lsn[u], lsn[v], l, mid, (mulu + 1ll * P1 * vvr % mod) % mod, (mulv + 1ll * P1 * vur % mod) % mod, P);
        rsn[u] = merge(rsn[u], rsn[v], mid + 1, r, (mulu + 1ll * P * vvl % mod) % mod, (mulv + 1ll * P * vul % mod) % mod, P);
        pushup(u);
        return u;
    }

}T;

int rt[maxn];

void dfs(int p) {
    if(vc[p].size() == 0) return T.Insert(rt[p], 1, len, val[p], 1);
    for(int v : vc[p]) dfs(v);
//    if(p == 1) {
//        for(int i = 1; i <= tot; ++ i) printf("%d : %d\n", i, t[i]);
//    }
    if(vc[p].size() == 1) return rt[p] = rt[vc[p][0]], void();
    if(vc[p].size() == 2) return rt[p] = T.merge(rt[vc[p][0]], rt[vc[p][1]], 1, len, 0, 0, ::p[p]), void();
}

void getans(int p,int l,int r) {
    if(!p) return ;
    if(l == r) return ansp[l] = t[p], void();
    T.pushdown(p);
    getans(ls, l, mid), getans(rs, mid + 1, r);
}

#undef ls
#undef rs
#undef mid

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) {
        int x; r1(x); if(i == 1) continue;
        vc[x].push_back(i);
    }
    for(i = 1; i <= n; ++ i) {
        int x; r1(x);
//        printf("%u\n", vc[i].size());
        if(vc[i].size() == 0) val[i] = x, c[++ ed] = x;
        else p[i] = 1ll * x * Inv % mod;
    }
//    for(i = 1; i <= n; ++ i) printf("%d : %u\n", i, vc[i].size());
    sort(c + 1, c + ed + 1);
    len = unique(c + 1, c + ed + 1) - c - 1;
    for(i = 1; i <= n; ++ i) if(p[i] == 0) val[i] = lower_bound(c + 1, c + len + 1, val[i]) - c;
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, p[i]);
//    printf("len = %d\n", len);

//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, val[i]);

    dfs(1);

    getans(rt[1], 1, len);
//    for(i = 1; i <= tot; ++ i) printf("%d : %d\n", i, t[i]);
//    printf("tot = %d, %d\n", tot, rt[1]);
//    for(i = 1; i <= len; ++ i) printf("%d : %d\n", i, ansp[i]);
    int ans(0);
    for(i = 1; i <= len; ++ i) ans = (ans + 1ll * i * c[i] % mod * ansp[i] % mod * ansp[i] % mod) % mod;
    printf("%d\n", ans);
	return 0;
}

```


