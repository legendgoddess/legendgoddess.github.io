---
title: 「SDOI2017」切树游戏
date: 2021-09-28 18:33:30
tags:
  - Dp
  - FWT
categories:
  - 省选题解
---

<h3><center>LOJ #2269. 「SDOI2017」切树游戏</center></h3>

> [#2269. 「SDOI2017」切树游戏](https://loj.ac/p/2269)

> 没错我在某谷被卡掉了，之后会了全局平衡二叉树再更新那个做法。

不考虑修改怎么做。

设 $f(i, j)$ 表示以 $i$ 为根的子树（必须包含点 $i$）异或值为 $j$ 的方案数， $G(i, j)$ 以 $i$ 为根的子树的答案。

考虑更新。对于上一个答案 $f'(i, j)$ 当前儿子 $v$ 可以更新得到：
$$
\begin{aligned}
f(i, j) = \sum_{j = k \otimes x} f'(i, k) \times (f(v, x) + 1)
\end{aligned}
$$
其中 $\otimes$ 是异或运算，显然我们可以使用 $fwt$ 进行优化，不妨考虑 $f(i)$ 表示已经进行 $fwt$ 后的答案，$G$ 也是如此。这样我们最后只要 $ifwt$ 即可。

那么转移方程显然有 $f(i) = f'(i) \times (f(v) + one)$。

$G(i) = G'(i) + (f(v) + one)$ 之后 $G$ 别忘了加上当前位置的贡献。

显然复杂度是 $O(nm)$。

因为每一个点被 $fwt$ 一次是一个 $\log $ 的。我们可以预处理每个 $a_i$ 的 $fwt$ 的结果。

------

之后我们考虑动态的情况，显然树链剖分一下，对于重儿子的转移我们直接通过轻儿子的矩阵进行转移即可。

那么我们需要设 $g(i)$ 表示 $G(i)$ 除了自己的重儿子的贡献，$f(i)$ 的定义同理。

考虑不妨考虑当前节点 $u$ 的重儿子 $v$ 的贡献也就是：
$$
\begin{aligned}
F(u) &= F(v) \times f(u) + f(u) \\
G(u) &= G(v) + g(u) + F(u) \\
G(u) &= G(v) + g(u) + F(v) \times f(u) + f(u)
\end{aligned}
$$
我们考虑有转移矩阵：
$$
\left[
\begin{matrix}
F(v) & G(v) & 1
\end{matrix}
\right]
\times 
\left[
\begin{matrix}
f(u) & f(u) & 0 \\
0 & 1 & 0 \\
f(u) & g(u) + f(u) & 1
\end{matrix}
\right]
$$
那么我们只要维护一条链的转移矩阵即可。

发现只有 $4$ 个位置有用，拆开来直接保存即可。

对于最终的答案肯定是 ：

$$
\left[
\begin{matrix}
0 & 0 & 1
\end{matrix}
\right]
\times 
\left[
\begin{matrix}
f(u) & f(u) & 0 \\
0 & 1 & 0 \\
f(u) & g(u) + f(u) & 1
\end{matrix}
\right]=
\left[
\begin{matrix}
f(u) & g(u) + f(u) & 1
\end{matrix}
\right]
$$

那么我们拆开变成 $4$ 个值的时候直接取下面的两个作为 $f$ 和 $g$ 即可。

但是这题还有很阴间的一点就是转移有乘法，肯定会出现是模数倍数的情况也就是没有逆元。我们考虑对于每一个点的每个儿子以及自身维护一个转移的值，也就是 $F(v) + 1$。每次修改即可。

感觉代码中难想的地方就是跳链更新，笔者具体说一下：

不妨假设点 $u$ 的权值改成 $c$。

显然先修改位置 $u$。

之后开始跳链，先修改一下当前的转移矩阵，如果有父亲那就将其的 $g$ 减去上一次的贡献，然后修改一下父亲的记录权值的线段树，将当前的 $G$ 修改即可，然后再更新上面的 $g$。

```cpp
#include <bits/stdc++.h>
using namespace std;

namespace io {
    const int SIZE = (1 << 21) + 1;
    char ibuf[SIZE], *iS, *iT, obuf[SIZE], *oS = obuf, *oT = oS + SIZE - 1, c, qu[55];
    int f, qr;
#define gc() (iS == iT ? (iT = (iS = ibuf) + fread (ibuf, 1, SIZE, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
    inline void flush () {
        fwrite (obuf, 1, oS - obuf, stdout);
        oS = obuf;
    }
    inline void putc (char x) {
        *oS ++ = x;
        if (oS == oT) flush ();
    }
    template <class I>
    inline void r1 (I &x) {
        x = 0;
        int f = 1;
        for (c = gc(); c < '0' || c > '9'; c = gc()) if(c == '-') f = -1;
        for (x = 0; c <= '9' && c >= '0'; c = gc()) x = (x * 10) + (c & 15);
        x *= f;
    }
    void rs(char *s) {
        char c = gc();
        while(!('A' <= c && c <= 'Z')) c = gc();
        while('A' <= c && c <= 'Z') *s ++ = c, c = gc();
        *s = 0;
    }
    template <class I>
    inline void print (I x) {
        if (!x) putc ('0');
        if (x < 0) putc ('-'), x = -x;
        while (x) qu[++ qr] = x % 10 + '0',  x /= 10;
        while (qr) putc (qu[qr --]);
    }
    struct Flusher_ {
        ~Flusher_() {
            flush();
        }
    } io_flusher_;
}
using io :: r1;
using io :: rs;
using io :: putc;
using io :: print;

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 3e4 + 5;
const int maxm = (1 << 7) + 5;
const int mod = 1e4 + 7;
const int inv2 = 5004;

vector<int> vc[maxn];

inline int inc(int a,int b) {return a += b - mod, a + ((a >> 31) & mod);}
inline int dec(int a,int b) {return a -= b, a + ((a >> 31) & mod);}
inline int mul(int a,int b) {return 1ll * a * b % mod;}

void add(int u,int v) {
    vc[u].push_back(v);
}

int n, m;

struct Poly {
    int A[maxm];

    void XOR() {
        for(int mid = 1; mid < m; mid <<= 1) {
            for(int j = 0, c = (mid << 1); j < m; j += c) {
                for(int k = 0; k < mid; ++ k) {
                    int x = A[j + k], y = A[j + k + mid];
                    A[j + k] = inc(x, y);
                    A[j + k + mid] = dec(x, y);
                }
            }
        }
    }

    void IXOR() {
        for(int mid = 1; mid < m; mid <<= 1) {
            for(int j = 0, c = (mid << 1); j < m; j += c) {
                for(int k = 0; k < mid; ++ k) {
                    int x = A[j + k], y = A[j + k + mid];
                    A[j + k] = mul(inv2, inc(x, y));
                    A[j + k + mid] = mul(inv2, dec(x, y));
                }
            }
        }
    }

    Poly(void) { memset(A, 0, sizeof(int) * m); }
    Poly(int x) { memset(A, 0, sizeof(int) * m), A[x] = 1; XOR(); }
    int& operator [] (const int &x) { return A[x]; }
    const int& operator [] (const int &x) const { return A[x]; }

    Poly operator * (const Poly &z) const {
        Poly res;
        for(int i = 0; i < m; ++ i) res[i] = mul(A[i], z.A[i]);
        return res;
    }
    Poly& operator *= (const Poly &z) {
        return *this = *this * z, *this;
    }
    Poly operator + (const Poly &z) const {
        Poly res;
        for(int i = 0; i < m; ++ i) res.A[i] = inc(A[i], z.A[i]);
        return res;
    }
    Poly& operator += (const Poly &z) {
        return *this = *this + z, *this;
    }
    Poly operator - (const Poly &z) const {
        Poly res;
        for(int i = 0; i < m; ++ i) res.A[i] = dec(A[i], z.A[i]);
        return res;
    }
    Poly& operator -= (const Poly &z) {
        return *this = *this - z, *this;
    }

    void operator = (const Poly &T) { memcpy(A, T.A, sizeof(int) * m); }

}one, F[maxn], G[maxn], g[maxn];

int rt[maxn], ct[maxn];

struct Node1 {
    int l, r, siz;
    Poly val;
}t[maxn * 10];

struct Seg1 {
    #define ls t[p].l
    #define rs t[p].r
    #define mid ((l + r) >> 1)
    int tot;
    Seg1(void) { tot = 0; }
    void pushup(int p) {
        t[p].val = t[ls].val * t[rs].val;
    }
    void Insert(int &p,int l,int r,int pos,const Poly &c) {
        if(!p) p = ++ tot;
        ++ t[p].siz;
        if(l == r) return t[p].val = c, void();
        if(pos <= mid) Insert(ls, l, mid, pos, c);
        else Insert(rs, mid + 1, r, pos, c);
        if(r - l < t[p].siz) pushup(p);
    }
    #undef ls
    #undef rs
    #undef mid
}vT;

int son[maxn], siz[maxn], bot[maxn], fa[maxn], dfn[maxn], fdfn[maxn], top[maxn];
int dfntot;

void dfs1(int p,int pre) {
    siz[p] = 1, fa[p] = pre;
    for(int v : vc[p]) if(v != pre) {
        dfs1(v, p);
        siz[p] += siz[v];
        if(siz[v] > siz[son[p]]) son[p] = v;
    }
}

void dfs2(int p,int pre,int topf) {
    top[p] = topf;
    dfn[p] = ++ dfntot, fdfn[dfntot] = p;
    if(son[p]) dfs2(son[p], p, topf), bot[p] = bot[son[p]];
    else bot[p] = p;
    for(int v : vc[p]) if(v != pre && v != son[p]) {
        dfs2(v, p, v);
    }
}

int a[maxn], pos[maxn];

void dfs3(int p,int pre) {
    F[p] = Poly(a[p]);
    for(int v : vc[p]) if(v != pre) {
        dfs3(v, p);
        F[p] *= (F[v] + one);
        G[p] += G[v];
    }
    G[p] += F[p];
    for(int v : vc[p]) if(v != pre && v != son[p]) {
        pos[v] = ++ ct[p];
    }
    ++ ct[p];
    vT.Insert(rt[p], 1, ct[p], ct[p], Poly(a[p]));
    for(int v : vc[p]) if(v != pre && v != son[p]) {
        g[p] += G[v];
        vT.Insert(rt[p], 1, ct[p], pos[v], F[v] + one);
    }
}

struct Node {
    Poly A, B, C, D;
    Node(void) {}
    Node(const Poly &a,const Poly &b,const Poly& c,const Poly& d) : A(a), B(b), C(c), D(d) {}
    Node(const Poly &f,const Poly &g) : A(f), B(f), C(f), D(f + g) {}
    Poly f() { return C; }
    Poly g() { return D; }
}tt[maxn << 2];

Node merge(Node A, Node B) {
    return Node(
                A.A * B.A,
                A.A * B.B + A.B,
                B.A * A.C + B.C,
                A.C * B.B + A.D + B.D
                );
}

int lson[maxn << 2], rson[maxn << 2], md[maxn << 2];

struct Seg2 {
    #define ls lson[p]
    #define rs rson[p]
    #define mid md[p]
    void build(int p,int l,int r) {
        if(l == r) {
            int id = fdfn[l];
            tt[p] = Node( t[rt[id]].val, g[id] );
            return ;
        }
        lson[p] = (p << 1), rson[p] = (p << 1 | 1), md[p] = (l + r) >> 1;
        build(ls, l, mid), build(rs, mid + 1, r);
        tt[p] = merge(tt[rs], tt[ls]);
    }

    void change(int p,int l,int r,int pos, const Node &c) {
        if(l == r) return tt[p] = c, void();
        if(pos <= mid) change(ls, l, mid, pos, c);
        else change(rs, mid + 1, r, pos, c);
        tt[p] = merge(tt[rs], tt[ls]);
    }

    Node Ask(int p,int l,int r,int ll,int rr) {
        if(ll <= l && r <= rr) return tt[p];
        if(rr <= mid) return Ask(ls, l, mid, ll, rr);
        else if(mid < ll) return Ask(rs, mid + 1, r, ll, rr);
        else return merge(Ask(rs, mid + 1, r, ll, rr), Ask(ls, l, mid, ll, rr));
    }

    #undef ls
    #undef rs
    #undef mid

}T;

void change(int u,int c) {
    a[u] = c;
    vT.Insert(rt[u], 1, ct[u], ct[u], Poly(a[u]));
    while(u) {
        T.change(1, 1, n, dfn[u], Node(t[rt[u]].val, g[u]));
        u = top[u];
        Node tmp = T.Ask(1, 1, n, dfn[u], dfn[bot[u]]);
        if(fa[u]) g[fa[u]] -= G[u];
        if(fa[u]) vT.Insert(rt[fa[u]], 1, ct[fa[u]], pos[u], tmp.f() + one);
        G[u] = tmp.g();
        if(fa[u]) g[fa[u]] += G[u];
        u = fa[u];
    }
}

int Ask(int x) {
    Poly tmp = T.Ask(1, 1, n, dfn[1], dfn[bot[1]]).g();
    tmp.IXOR();
    return tmp[x];
}

char s[20];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
//    puts("YES");
    r1(n, m);
    for(i = 0; i < m; ++ i) one[i] = 1;
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i < n; ++ i) {
        int u, v;
        r1(u, v), add(u, v), add(v, u);
    }
    dfs1(1, 0), dfs2(1, 0, 1), dfs3(1, 0);
    T.build(1, 1, n);
    int Q;
    r1(Q);
    while(Q --) {
        rs(s + 1);
//        scanf("%s", s + 1);
        int x, y;
        r1(x);
        if(s[1] == 'C') {
            r1(y);
            change(x, y);
        }
        else {
            print(Ask(x));
            putc('\n');
        }
    }
	return 0;
}
```








