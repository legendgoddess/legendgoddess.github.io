---
title: 全局平衡二叉树
date: 2021-10-01 212911
tags: 
  - 平衡树
categories: 
  - 算法
---

<h3><center>全局平衡二叉树 P4751 【模板】"动态DP"</center></h3>

> [P4751 【模板】"动态DP"&动态树分治（加强版）](https://www.luogu.com.cn/problem/P4751)

>  有事没事就用 $\tt vector$ 总会有天废掉的。

> 注意在 $\tt c++11$ 之后 $\tt vector$ 不管是是否在 $O2$ 的条件下，速度都比常规数组慢很多，甚至比链表要慢 $4$ 倍。

死亡写法：

```cpp
struct Tree : vector<vector<int>> {
    Tree() : vector<vector<int>>(2, vector<int>(2, -inf)) {}
    Tree operator * (const Tree &z) {
        Tree res;
        for(int i = 0; i < 2; ++ i) for(int j = 0; j < 2; ++ j) for(int k = 0; k < 2; ++ k)
            res[i][j] = max(res[i][j], this->at(i)[k] + z[k][j]);
        return res;
    }
};
```

> 要是闲着搞类继承没救了。

---

发现树剖有两个 $\log $ 我们发现 $\tt LCT$ 只有一个 $\log$ 但是这个常数确实有点大。

因为树是静态的，我们直接树链剖分之后建立一个静态的 $\tt Bst$ 即可。

至于具体的形态其实和 $\tt LCT$ 是一样的，只是我们每次当做根节点的都是带权重心，权值是轻子树大小。

之后转移的方法还是和树链剖分一样，但是这里需要维护两个数组，因为不能每次都直接像线段树一样从下到上进行转移，也就是对于每一个点特殊保留一个轻儿子的矩阵。

可以发现对于全局来说这个树是联通的而且最深处也只有 $\log n$。那么我们直接暴力跳父亲进行合并复杂度就是一个 $\log $ 的。

顺便说一下定义：

- $f(i, 0/ 1)$ 对于当前子树，当前点是否选择的最大权值。
- $g(i, 0/1)$ 对于当前轻儿子，当前点是否选择的最大权值。
- $a(i)$ 点权。
- $lsiz(i)$ 点 $i$ 轻儿子大小。

不妨设 $v$ 的父亲是 $u$，$v, u$ 在同一条重链上，矩阵转移方程：
$$
\left[
\begin{matrix}
f(v, 0) & f(v, 1)
\end{matrix}
\right]
\times 
\left[
\begin{matrix}
g(u, 0) & g(u, 1) + a(u) \\
g(u, 0) & - \infty
\end{matrix}
\right]
$$

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

//#define int long long
const int maxn = 1e6 + 5;
const int maxm = maxn << 1;

int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}
int a[maxn];
int n, m;

#define max Max
inline int Max(const int& a, const int& b) { return a > b ? a : b; }
const int inf = 0x3f3f3f3f;
struct Tree {
    int a[2][2];
    Tree(void) { memset(a, -0x3f, sizeof(a)); }
    int* operator [] (const int& x) { return a[x]; }
    const int* operator [] (const int&x) const { return a[x]; }
    Tree operator * (const Tree &z) {
        Tree res;
        for(int i = 0; i < 2; ++ i) for(int j = 0; j < 2; ++ j) for(int k = 0; k < 2; ++ k)
            res[i][j] = max(res[i][j], a[i][k] + z[k][j]);
        return res;
    }
};

int f[maxn][2], g[maxn][2];
int son[maxn], siz[maxn], lsiz[maxn];
void dfs(int p,int pre) {
    siz[p] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int v = edg[i].to; if(v == pre) continue;
        dfs(v, p), siz[p] += siz[v];
        if(siz[v] > siz[son[p]]) son[p] = v;
    }
    lsiz[p] = siz[p] - siz[son[p]];
}

void dfs1(int p,int pre) {
    f[p][1] = g[p][1] = a[p];
    f[p][0] = g[p][0] = 0;
    if(son[p]) {
        dfs1(son[p], p),
        f[p][0] += max(f[son[p]][0], f[son[p]][1]),
        f[p][1] += f[son[p]][0];
    }
    else return ;
    for(int i = head[p];i;i = edg[i].next) {
        int v = edg[i].to; if(v == pre || v == son[p]) continue;
        dfs1(v, p),
        f[p][0] += max(f[v][0], f[v][1]),
        g[p][0] += max(f[v][0], f[v][1]),
        f[p][1] += f[v][0], g[p][1] += f[v][0];
    }
}

int fa[maxn], vis[maxn];
int st[maxn];
int sn[maxn][2];
Tree v[maxn], vy[maxn];

void pushup(int p) {
    v[p] = vy[p];
    if(sn[p][0])
        v[p] = v[p] * v[sn[p][0]];
    if(sn[p][1])
        v[p] = v[sn[p][1]] * v[p];
}

int Sbuild(int l,int r) {
//    printf("l = %d, r = %d\n", l, r);
    if(l > r) return 0;
    int tot(0);
    for(int i = l; i <= r; ++ i) tot += lsiz[st[i]];
    for(int i = l, sum(lsiz[st[l]]); i <= r; ++ i, sum += lsiz[st[i]]) if((sum << 1) >= tot) {
        int L = Sbuild(l, i - 1), R = Sbuild(i + 1, r);
        sn[st[i]][0] = L, sn[st[i]][1] = R;
        fa[L] = fa[R] = st[i];
        pushup(st[i]);
        return st[i];
    }
    return 0;
}

int build(int p) {
    for(int u = p; u; u = son[u]) vis[u] = 1;
    for(int u = p, ret; u; u = son[u]) {
        for(int i = head[u];i;i = edg[i].next) {
            int v = edg[i].to; if(vis[v]) continue;
            ret = build(v);
            fa[ret] = u;
        }
    }
    int tot(0);
    for(int u = p; u; u = son[u]) st[++ tot] = u;
    int ret = Sbuild(1, tot);
    return ret;
}

inline int getmx0(const int& p) {
    return max(v[p][0][0], v[p][1][0]);
}

inline int getmx1(const int& p) {
    return v[p][0][1];
}

void Modify(int u, int c) {
    vy[u][0][1] -= a[u] - c; a[u] = c;
    for(int f; f = fa[u], u; u = fa[u]) if(f && u != sn[f][0] && u != sn[f][1]) {
        vy[f][0][0] -= max(getmx1(u), getmx0(u));
        vy[f][1][0] = vy[f][0][0];
        vy[f][0][1] -= getmx0(u);
        pushup(u);
        vy[f][0][0] += max(getmx1(u), getmx0(u));
        vy[f][1][0] = vy[f][0][0];
        vy[f][0][1] += getmx0(u);

    } // light
    else pushup(u);
}

int root;

signed main() {
//    freopen("P4719_5.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i < n; ++ i) {
        int u, v; r1(u, v), add(u, v), add(v, u);
    }
    dfs(1, 0), dfs1(1, 0);
    for(i = 1; i <= n; ++ i) {
        vy[i][1][0] = vy[i][0][0] = g[i][0];
        vy[i][0][1] = g[i][1];
        vy[i][1][1] = -inf;
    }
    root = build(1);
    int lastans(0);
//    printf("rt = %d\n", root);
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, vis[i]);
//    for(i = 1; i <= n; ++ i) printf("%d : %d %d\n", i, ls[i], rs[i]);
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, fa[i]);
//    printf("res = %d, %d\n", Ask(), max(f[1][0], f[1][1]));
    while(m --) {
        int x, y;
        r1(x, y);
        x ^= lastans;
        Modify(x, y);
        printf("%d\n", lastans = max(getmx0(root), getmx1(root)));
    }
	return 0;
}
```




