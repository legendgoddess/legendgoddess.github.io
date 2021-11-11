---
title: CF1385E Directing Edges
date: 2021-09-30 07:49:19
tags:
  - 图论
  - 拓扑
categories:
  - CF题解
---

<h3><center>CF1385E Directing Edges</center></h3>

> [CF1385E Directing Edges](https://codeforces.ml/problemset/problem/1385/E)

**题目描述：**

给定一个图，有无向边和有向边。对于无向边进行定向，问能否给出一个方案使得定向后的图是无环的，图不一定要联通。

------

我们先不考虑无向边，对于有向边组成的图，不妨进行一次拓扑排序找到每一个节点遍历的先后顺序。

我们先判掉有环的情况，也就是存在一条边 $u \to v$ 而且点 $u$ 的遍历时间比 $v$ 要晚。

之后考虑我们肯定可以构造出一种合法的图，也就是对于所有的无向边我们考虑连接的两个几点，只要保证边 $u \to v$ 而且点 $u$ 的遍历时间比 $v$ 早即可。

具体实现的时候因为图不一定联通，所以我们不妨对于每一个节点进行 $\tt dfs$ 在搜索完儿子之后再将自己加入。这样可以保证及时搜索节点的顺序不同也可以保证是拓扑序列。

> 之后取反即可，因为现在是逆着的拓扑序。

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
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

vector<vector<int> > vc;

void add(int u,int v) {
    vc[u].push_back(v);
}
int n, m;

struct Edge {
    int opt, u, v;
}E[maxn];
int tot(0);

void Solve() {
    int i, j;
    r1(n, m); tot = 0;
    vc.clear(), vc.resize(n + 1);
    for(i = 1; i <= m; ++ i) {
        int opt, u, v;
        r1(opt, u, v);
        if(opt == 1) add(u, v);
        E[++ tot] = (Edge) {opt, u, v};
    }
    vector<int> vis(n + 1, 0), od, pos(n + 1, 0);
    function<void(int)> dfs;
    dfs = [&] (int p) {
        vis[p] = 1;
        for(int v : vc[p]) if(!vis[v]) dfs(v);
        od.push_back(p);
    };
    for(i = 1; i <= n; ++ i) if(!vis[i]) dfs(i);
    reverse(od.begin(), od.end());
    for(i = 0; i < n; ++ i) pos[od[i]] = i;
    for(i = 1; i <= n; ++ i) for(int v : vc[i]) if(pos[i] > pos[v]) return puts("NO"), void();
    puts("YES");
    for(i = 1; i <= m; ++ i) {
        if(pos[E[i].u] > pos[E[i].v]) swap(E[i].u, E[i].v);
        printf("%d %d\n", E[i].u, E[i].v);
    }
    return ;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, T;
    r1(T);
    while(T --) Solve();
	return 0;
}
```




