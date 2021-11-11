---
title: 浅谈线段树合并
date: 2021-11-11 21:19:00
tags:
  - 线段树
  - 数据结构
categories:
  - 算法
---

<h3><center>浅谈线段树合并</center></h3>

> 观前提示：本文主要介绍线段树合并的浅层应用和较为简单的写法。

<h4><center>权值线段树</center></h4>

我们线段树的很多操作都是基于**动态开点**权值线段树进行实现的，空间是 $O(n \log n \times K)$ 其中 $K$ 是每次询问进行线段树操作的个数。当然我们有优化到 $O(n)$ 空间的做法，这个我们后面再谈。

<h4><center>维护方式</center></h4>

我们通常将其用来维护一些可以通过全局表示的信息，最显然的就是一段值域，或者是深度等。

线段树合并也可以在线进行维护信息的，也就是我们对于每个线段树都要保留下来，我们像可持久化一样，每次进行操作的时候就复制一份节点，这个空间就是 $O(n\text{ polylog(n)})$。

<h4><center>具体做法</center></h4>

考虑合并的时候贪心一下，如果说两棵树其中有一个是空的，那么我们直接接到另外一个树上即可。

这里推荐一种写法：

```cpp
int merge(int u,int v) {
    if(!u || !v) return u | v;
    if(isleaf(u)) {
        Add(v, val[u]);
        return v;
    }
    if(isleaf(v)) {
        Add(u, val[v]);
        return u;
    }
    pushdown(u), pushdown(v);
    lson[u] = merge(lson[u], lson[v]);
    rson[u] = merge(rson[u], rson[v]);
    pushup(u);
}
```

这样方便我们之后优化卷积。

<h4><center>空间优化</center></h4>

考虑我们每次遍历最大的子树，这样最坏的情况总共只有 $\log_2n$ 个线段树没有合并，就只有 $\frac{n}{2} + \frac{n}{4} + \frac{n}{8} + \dots + \frac{n}{2^{\log_2n}}$ 个叶子节点有权值。

我们假设 $n = 2^x$。

考虑每个线段树最上层肯定是满的，下面就是 $\log n$ 的空间，我们直接进行计算即可。
$$
\begin{aligned}
&\sum_{i = 1} ^ {x} 2^{x - i} + i \times 2^{x - i} \\
=& O(n) + \sum_{i = 1} ^ x (x - i) \times 2^i \\
=& O(n) + O(n \log n) - O(n \log n) \\
=& O(n)
\end{aligned}
$$


<h4><center>基础例题</center></h4>

1. [P4556 [Vani有约会]雨天的尾巴 /【模板】线段树合并](https://www.luogu.com.cn/problem/P4556)

   > 可以使用树剖加线段树维护，但是我们只需要差分一下维护值域即可。

2. [P3899 [湖南集训]更为厉害](https://www.luogu.com.cn/problem/P3899) 

   > 分类讨论，发现只有一类是和深度有关的，我们直接维护全局深度即可。

3. [P5384 [Cnoi2019]雪松果树](https://www.luogu.com.cn/problem/P5384)

   > 同上题，但是略微有点卡空间，直接使用 $O(n)$ 优化即可。

<h4><center>优化卷积</center></h4>

我们直接考虑将左右两边子树的贡献记录下传即可。

这里具体可以优化的形式是 $\oplus \sum_{i = 1} ^ n \sum_{j = i + 1} ^ n val_i \otimes val_j$。

同样我们不要拘泥于 $\sum$ 的下标，这里只是具体表示只能维护 $ogf$ 卷积的操作而已。

这里放一题代码：

[[PKUWC2018]Minimax](https://www.luogu.com.cn/problem/P5298)

```cpp
#include <bits/stdc++.h>
using namespace std;

#define Fread
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

const int mod  = 998244353;
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

//#define int long long
const int maxn = 3e5 + 5;
const int maxm = maxn * 10;

Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}
const Tm Inv = ksm(Tm(10000), mod - 2);

int n;
Tm p[maxn];

int tot(0);
int vc[maxn][2], son[maxn];
Tm t[maxm], tag[maxm];
int lsn[maxm], rsn[maxm];
int rt[maxn];
Tm ansp[maxn];
struct Seg {
    #define ls lsn[p]
    #define rs rsn[p]

    void pushup(int p) {
        t[p] = (t[ls] + t[rs]);
    }

    void Add(int p,Tm c) {
        if(p == 0) return ;
        t[p] = t[p] * c;
        tag[p] = tag[p] * c;
    }

    void pushdown(int p) {
        if(tag[p].z == 1)  return ;
        Add(ls, tag[p]), Add(rs, tag[p]);
        tag[p] = 1;
    }

    void Insert(int &p,int l,int r,int pos,int c) {
        if(!p) p = ++ tot, tag[p] = 1;
        if(l == r) return t[p] = c, void();
        int mid = (l + r) >> 1;
        if(pos <= mid) Insert(ls, l, mid, pos, c);
        else Insert(rs, mid + 1, r, pos, c);
        pushup(p);
    }

    int merge(int u,int v,int l,int r,Tm mulu,Tm mulv,const Tm &P) {
//        printf("%d %d, (%d, %d), %d %d\n", u, v, l, r, mulu, mulv);
        if(!u) return Add(v, mulv), v;
        if(!v) return Add(u, mulu), u;
        pushdown(u), pushdown(v);
        Tm vul = t[lsn[u]], vur = t[rsn[u]], vvl = t[lsn[v]], vvr = t[rsn[v]];
        const Tm P1 = (Tm(1) - P);
        int mid = (l + r) >> 1;
        lsn[u] = merge(lsn[u], lsn[v], l, mid, mulu + P1 * vvr, mulv + P1 * vur, P);
        rsn[u] = merge(rsn[u], rsn[v], mid + 1, r, mulu + P * vvl, mulv + P * vul, P);
        pushup(u);
        return u;
    }


}T;

int val[maxn], ed(0);
int c[maxn], len;

void dfs(int p) {
    if(son[p] == 0) return T.Insert(rt[p], 1, len, val[p], 1);
    for(int i = 0; i < son[p]; ++ i) dfs(vc[p][i]);
    if(son[p] == 1) return rt[p] = rt[vc[p][0]], void();
    if(son[p] == 2) return rt[p] = T.merge(rt[vc[p][0]], rt[vc[p][1]], 1, len, 0, 0, ::p[p]), void();
}

void getans(int p,int l,int r) {
    if(!p) return ;
    if(l == r) return ansp[l] = t[p], void();
    T.pushdown(p);
    int mid = (l + r) >> 1;
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
        vc[x][son[x] ++] = i;
    }
    for(i = 1; i <= n; ++ i) {
        int x; r1(x);
        if(son[i] == 0) val[i] = x, c[++ ed] = x;
        else p[i] = Inv * x;
    }
    sort(c + 1, c + ed + 1);
    len = unique(c + 1, c + ed + 1) - c - 1;
    for(i = 1; i <= n; ++ i) if(p[i].z == 0) val[i] = lower_bound(c + 1, c + len + 1, val[i]) - c;
    dfs(1);
    getans(rt[1], 1, len);
    Tm ans(0);
    for(i = 1; i <= len; ++ i) ans += Tm(i) * ansp[i] * ansp[i] * c[i];
    printf("%d\n", ans.z);
	return 0;
}
```



