---
title: Codechef Dynamic GCD
date: 2021-09-15 19:21:33
tags: 
  - 数据结构
categories:
  - CODECHEF题解
---


<h3> <center> Codechef Dynamic GCD </center></h3>

> [Codechef Dynamic GCD](https://www.codechef.com/problems/DGCD)

就是考虑维护链上 $\gcd$ 和链加。我们先考虑这个东西放到序列上怎么做，首先进行差分，之后因为 $\gcd$ 是不变的，但是区间加可以直接变成了单点修改用线段树进行维护即可。

---

对于树上的情况，我们直接使用树链剖分进行分成若干条链进行计算。

具体来说我们考虑对于一条链 $u, v, fa_u = v$，我们让节点 $u$ 的值变成 $a_v - a_u$ 即可。对于链头的情况我们直接作为原来的值即可。

但是会有一个问题，如果我们一条链没有被使用完成我们就需要知道最上面的那个值。

对于这个我们只需要维护一下被加的值即可，为了方便使用了标记永久化。

----

一些细节：

- 根据我们差分的定义，对于一个节点 $u$ 增加了 $c$ 的时候，其单点维护的 $\gcd$ 是要 $- c$ 的，其儿子是要 $+ c$ 的。当然对于 $u = \tt{top}_u$ 的情况例外。
- 我们进行最终计算答案的时候不要忘记计算最上面一个点的贡献，也不要多计算。

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
const int maxn = 5e4 + 5;
const int maxm = maxn << 1;

int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) { v, head[u] }, head[u] = cnt;
}

int n, m;
int son[maxn], fa[maxn], top[maxn], siz[maxn];

void dfs1(int p,int pre) {
    siz[p] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        dfs1(to, p);
        siz[p] += siz[to];
        if(siz[to] > siz[son[p]]) son[p] = to;
    }
}
int dfn[maxn], dfntot, fdfn[maxn], dep[maxn];
int v[maxn], a[maxn];
void dfs2(int p,int pre,int topf) {
    dep[p] = dep[pre] + 1;
    dfn[p] = ++ dfntot;
    fdfn[dfntot] = p;
    fa[p] = pre;
    top[p] = topf;
    if(son[p]) dfs2(son[p], p, topf);
    else return ;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre || to == son[p]) continue;
        dfs2(to, p, to);
    }
}

int gcd(int a,int b) {
    return b == 0 ? a : gcd(b, a % b);
}

struct Node {
    int gd, val;
    Node(void) {gd = val = 0;}
}t[maxn << 2];

struct Seg {
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid ((l + r) >> 1)

    void pushup(int p) {
        t[p].gd = gcd(t[ls].gd, t[rs].gd);
    }

    void build(int p,int l,int r) {
        if(l == r) {
            if(top[fdfn[l]] == fdfn[l]) return t[p].gd = v[fdfn[l]], void();
            else return t[p].gd = v[fa[fdfn[l]]] - v[fdfn[l]], void();
        }
        build(ls, l, mid), build(rs, mid + 1, r);
        pushup(p);
    }

    void ChangeGcd(int p,int l,int r,int pos,int c) {
        if(l == r) return t[p].gd += c, void();
        if(pos <= mid) ChangeGcd(ls, l, mid, pos, c);
        else ChangeGcd(rs, mid + 1, r, pos, c);
        pushup(p);
    }

    void ChangeVal(int p,int l,int r,int ll,int rr, int c) {
        assert(ll <= rr);
        if(ll <= l && r <= rr) return t[p].val += c, void();
        if(ll <= mid) ChangeVal(ls, l, mid, ll, rr, c);
        if(mid < rr) ChangeVal(rs, mid + 1, r, ll, rr, c);
    }

    int AskVal(int p,int l,int r,int pos) {
        if(l == r) return v[fdfn[l]] + t[p].val;
        if(pos <= mid) return AskVal(ls, l, mid, pos) + t[p].val;
        else return AskVal(rs, mid + 1, r, pos) + t[p].val;
    }

    int AskGcd(int p,int l,int r,int ll,int rr) {
        if(ll > rr) return 0;
        if(ll <= l && r <= rr) return t[p].gd;
        int res(0);
        if(ll <= mid) res = AskGcd(ls, l, mid, ll, rr);
        if(mid < rr) res = gcd(res, AskGcd(rs, mid + 1, r, ll, rr));
        return res;
    }

}T;

void ChangeList(int u,int v,int c) {
    if(u == v) {
        if(u != top[u]) T.ChangeGcd(1, 1, n, dfn[u], - c);
        else T.ChangeGcd(1, 1, n, dfn[u], c);

        if(son[u]) T.ChangeGcd(1, 1, n, dfn[son[u]], c);

        T.ChangeVal(1, 1, n, dfn[u], dfn[u], c);
        return ;
    }

    while(top[u] != top[v]) {
        if(dep[top[u]] < dep[top[v]]) swap(u, v);
        T.ChangeVal(1, 1, n, dfn[top[u]], dfn[u], c);
        T.ChangeGcd(1, 1, n, dfn[top[u]], c);
        if(son[u]) T.ChangeGcd(1, 1, n, dfn[son[u]], c);
        u = fa[top[u]];
    }

    if(dep[u] < dep[v]) swap(u, v);
    T.ChangeVal(1, 1, n, dfn[v], dfn[u], c); // 加上权值即可

    if(v != top[v]) T.ChangeGcd(1, 1, n, dfn[v], - c);
    else T.ChangeGcd(1, 1, n, dfn[v], c);// 这里其实和上面一样的
    if(son[u] != 0) T.ChangeGcd(1, 1, n, dfn[son[u]], c);
}

int AskList(int u,int v) {
    if(u == v) return T.AskVal(1, 1, n, dfn[u]);
    int res(0);
    while(top[u] != top[v]) {
        if(dep[top[u]] < dep[top[v]]) swap(u, v);
        res = gcd(res, T.AskGcd(1, 1, n, dfn[top[u]], dfn[u]));
        u = fa[top[u]];
    }
    if(dep[u] < dep[v]) swap(u, v);
    if(u != v)
        res = gcd(res, T.AskGcd(1, 1, n, dfn[v] + 1, dfn[u]));
    res = gcd(res, T.AskVal(1, 1, n, dfn[v]));
    return res;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i < n; ++ i) {
        int u, v;
        r1(u, v);
        ++ u, ++ v;
        add(u, v), add(v, u);
    }
    for(i = 1; i <= n; ++ i) r1(v[i]);
    dfs1(1, 0);
    dfs2(1, 0, 1);
    T.build(1, 1, n);
    int Q;
    r1(Q);
    while(Q --) {
        char s[10]; int u, v, d;
        scanf("%s", s + 1);
        r1(u, v);
        ++ u, ++ v;
        if(s[1] == 'F') { // ask
            printf("%d\n", abs(AskList(u, v)));
        }
        else {
            r1(d);
            ChangeList(u, v, d);
        }
    }
	return 0;
}

/*

20
0 1
0 2
0 3
3 4
0 5
4 6
5 7
7 8
5 9
7 10
0 11
2 12
10 13
0 14
0 15
0 16
7 17
14 18
3 19
7 9 1 7 11 20 1 11 17 5 5 17 13 12 1 9 11 12 1 16
6
C 0 8 6
C 6 4 15
F 7 1
F 16 4
F 10 0
F 10 0

1
1
1
1
*/

```



