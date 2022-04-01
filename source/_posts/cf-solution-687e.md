---
title: "CF687E TOF 题解"
date: 2022-4-1 19:18:00
tags:
  - 图论
categories:
  - CF题解
---

[Problem - 687E - Codeforces](https://codeforces.com/problemset/problem/687/E)

图是不变的，如果出现环我们肯定是必须要一直遍历，所以我们肯定是考虑通过改变遍历顺序得到最小的环。

考虑缩点之后的强联通分量，如果一个强联通分量没有出边肯定是要自己内部构造环。

如果不然我们肯定是可以构造出一个到另外强联通分量的最短路径。

考虑一个环的贡献就是  $\text{点数} \times 999 + 1$。

对于全局来说就是 $\text{点数} \times 998 + n + \text{环数}$。

直接 $\tt tarjan$ 暴力做即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
//		#define Fread
		#ifdef Fread
		const int Siz = (1 << 21) + 5;
		char *iS, *iT, buf[Siz];
		#define gc() ( iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iS == iT ? EOF : *iS ++) : *iS ++ )
		#define getchar gc
		#endif
		template <typename T>
		void r1(T &x) {
		    x = 0;
			char c(getchar());
			int f(1);
			for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
			for(; isdigit(c); c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
			x *= f;
		}
		template <typename T, typename...Args>
		void r1(T &x, Args&...arg) {
			r1(x), r1(arg...);
		}
		#undef getchar
	}

using namespace Read;

const int maxn = 2e5 + 5;
int n, m;
int head[maxn], cnt(1);
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

int dfn[maxn], low[maxn], st[maxn], ed, in[maxn], bel[maxn];
vector<int> scc[maxn];
int stot(0), dfntot(0);

void tarjan(int p) {
    dfn[p] = low[p] = ++ dfntot, in[p] = 1, st[++ ed] = p;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        if(!dfn[to]) tarjan(to), low[p] = min(low[p], low[to]);
        else if(in[to]) low[p] = min(low[p], dfn[to]);
    }
    if(dfn[p] == low[p]) {
        int y; ++ stot;
        do {
            y = st[ed --];
            scc[stot].emplace_back(y);
            in[y] = 0, bel[y] = stot;
        } while(y != p);
    }
}

int dis[maxn];

int gloop(int x) {
    for(int v : scc[bel[x]]) dis[v] = 1e9;
    dis[x] = 0;
    static queue<int> q; while(!q.empty()) q.pop();
    q.push(x);
    while(!q.empty()) {
        int u = q.front(); q.pop();
        for(int i = head[u];i;i = edg[i].next) {
            int to = edg[i].to; if(bel[to] != bel[u]) continue;
            if(dis[to] > dis[u] + 1) {
//                printf("(%d, %d)\n", u, to);
                dis[to] = dis[u] + 1;
                q.push(to);
            }
        }
    }
    int res(2e9);
    for(int v : scc[bel[x]])
    for(int i = head[v];i;i = edg[i].next) {
        int to = edg[i].to;
        if(to == x) res = min(res, dis[v] + 1);
    }
//    printf("res = %d\n", res);
    return res;
}

signed main() {
	int i, j, ans(0);
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v;
        r1(u, v), add(u, v);
    }
    for(i = 1; i <= n; ++ i) if(!dfn[i]) tarjan(i);
//    printf("sz = %d\n", stot);
    for(i = 1; i <= stot; ++ i) {
        int sum(0);
        for(int v : scc[i])
            for(j = head[v];j;j = edg[j].next)
                if(i != bel[edg[j].to]) ++ sum;
        if(sum != 0) continue;
        if(scc[i].size() == 1) continue;
        int tmp(2e9);
        for(int v : scc[i]) tmp = min(tmp, gloop(v));
        ans += tmp * 998 + 1;
    }
    ans += n;
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


