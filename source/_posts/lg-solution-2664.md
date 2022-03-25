---
title: P2664 树上游戏
date: 2021-08-24 14:03:43
tags:
  - 数据结构
  - 分治
categories:
  - 洛谷题解
---


### [$\tt luogu\ P2664 \ 树上游戏$](https://www.luogu.com.cn/problem/P2664)

**题目大意：**

一棵树上每个节点都有颜色，给定一个长度为 $n$ 的颜色序列，定义 $s(i, j)$ 为 $i \to j$ 的颜色数量。

设 $sum_i = \sum_{j = 1} ^ n s(i, j)$。求所有的 $sum_i$。

---

这个本质可以看成树上的点对的问题。既然不考虑在线我们可以使用离线算法点分治。

我们需要在 $O(n)$ 或者 $O(n \log n)$ 的复杂度完成统计以当前节点为 $lca$ 的点对的所有贡献。

显然暴力统计是不行的。考虑借鉴一下统计树上距离为 $k$ 的点对的方法，开桶进行计算。

首先考虑以根节点为起点的方案数，也就是考虑每一条链上第一个出现的节点，其对于根的贡献是其子树大小。

> 其子树中每一个点都会产生一次这样颜色的贡献。

为了方便统计我们将根节点的颜色也加上贡献，同时提前打上标记。

之后需要统计点对的贡献。我们不妨将上一次产生贡献的点的贡献记成 $f(i)​$。那么不产生贡献显然是 $f(i) = 0​$。

既然是统计子树中的贡献。考虑遍历每一个子树。

对于每个点的贡献就是。设当前的颜色集合为 $S​$。点集合是 $T​$，子树的大小是 $Siz​$。

$$
\sum f - \sum_{v \in T} f(v) - \sum_{v \not\in T,col_v \in S} f(v) +\sum_{u \in S} (All - Siz)
$$

> 后面的就是外面的点对当前子树的总贡献。

**具体来说**：对于一个点 $v$ 可以变成 $x \to rt \to v$。显然直接统计会有重复的贡献。从 $x \to rt$ 显然就是不在当前子树的 $f$ 的贡献之和，也就是前面两项。然后这会产生重复的贡献，那么我们考虑换根，也就是最近的节点是当前遍历到的颜色和之前重复的节点，所以需要减去之前和当前颜色相同的节点的贡献，也就是第 $3$ 项，最后一项就是从 $rt \to v$ 的所有点的贡献次数。

我们实现的时候开桶记录颜色为 $x$ 的 $f$ 之和。每个子树的 $f$ 之和。然后开桶记录之前出现的颜色进行叠加即可。

**注意：** 根到每个节点的贡献我们还没有计算，在 $dfs$ 的时候顺便加入即可。

**注意：** 我们找到一个新的根的时候需要重新计算一下 $siz$。

> 说句闲话，感觉我之前写的点分治虽然复杂度对，但是挺假的。

之后我们还需要清空节点，变成一遍 $dfs$ 就好了。

```cpp
#include <bits/stdc++.h>
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

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) { v, head[u] }, head[u] = cnt;
}

int siz[maxn], mx[maxn];
int vis[maxn];
int Mx(2e9), rt, All;
void getrt(int p,int pre) {
    siz[p] = 1, mx[p] = 0;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre || vis[to]) continue;
        getrt(to, p);
        siz[p] += siz[to];
        mx[p] = max(mx[p], siz[to]);
    }
    mx[p] = max(All - siz[p], mx[p]);
    if(mx[p] < Mx) Mx = mx[p], rt = p;
}

int col[maxn], a[maxn];
long long colf[maxn]; // colf[i] 全树中颜色为 i 的点的 f 贡献
long long branf[maxn];// branf[i] 当前子树中 f 之和
long long brancolf[maxn];// brancolf[i] 当前子树中颜色为 i 的 f 之和
int tcol[maxn], af, tot(0); // 开桶记录全部所有的颜色，方便清空

void dfs1(int p,int pre,int br) { // 重新计算子树大小
    if(pre == rt) br = p;
    siz[p] = 1;
    int isn(0), cl(a[p]);
    if(!col[cl]) isn = col[cl] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre || vis[to]) continue;
        dfs1(to, p, br);
        siz[p] += siz[to];
    }
    if(isn) {
        if(!colf[cl])
            tcol[++ tot] = cl;
        colf[cl] += siz[p];
        branf[br] += siz[p]; // 记录子树中 f 的贡献
        af += siz[p]; // 计算所有 f 的贡献
        col[cl] = 0;
    }
}

void dfs2(int p,int pre,int br) {
    int cl(a[p]), isn(0);
    if(!col[cl]) isn = col[cl] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre || vis[to]) continue;
        dfs2(to, p, br);
    }
    if(isn) col[cl] = 0, brancolf[cl] += siz[p];
}

long long ans[maxn];

void dfs3(int p,int pre,int br) {
    ans[p] += All - siz[br];
    int cl(a[p]), isn(0);
    if(!col[cl]) {
        isn = col[cl] = 1;
        af -= (colf[cl] - brancolf[cl]);
        af += (All - siz[br]);
    }
    ans[p] += af;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre || vis[to])  continue;
        dfs3(to, p, br);
    }
    if(isn) {
        col[cl] = 0;
        af += (colf[cl] - brancolf[cl]);
        af -= (All - siz[br]);
    }

}

void dfs4(int p,int pre) { // 清空当前子树的颜色的 f
    brancolf[a[p]] = 0;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre || vis[to]) continue;
        dfs4(to, p);
    }
}

int n;

void dfs(int p) {

    vis[p] = 1;
    af = 0;

    tot = 0;
    tcol[++ tot] = a[p];
    col[a[p]] = 1;
    af = 0;

    dfs1(p, 0, 0);

    ans[p] += af + siz[p]; // 根的贡献
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(vis[to]) continue;
        af -= branf[to];
        dfs2(to, p, to);
        dfs3(to, p, to);
        dfs4(to, p);
        af += branf[to];
        branf[to] = 0;
    }
    col[a[p]] = 0;
    for(int i = 1; i <= tot; ++ i) colf[tcol[i]] = 0;
    tot = 0;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(vis[to]) continue;
        Mx = 2e9, All = siz[to];
        getrt(to, p);
        dfs(rt);
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i < n; ++ i) {
        int u, v;
        r1(u, v);
        add(u, v), add(v, u);
    }
    Mx = 2e9, All = n;
    getrt(1, 0);
    dfs(rt);
    for(i = 1; i <= n; ++ i) printf("%lld\n", ans[i]);
	return 0;
}
```




