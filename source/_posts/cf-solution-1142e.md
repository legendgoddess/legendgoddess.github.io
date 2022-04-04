---
title: "CF1142E Pink Floyd 题解"
date: 2022-4-4 13:48:00
tags:
  - 图论
  - 构造
categories:
  - CF题解
---

[Problem - 1142E - Codeforces](https://codeforces.com/problemset/problem/1142/E)

### 一点自己的想法

首先考虑 $m = 0$ 的情况，同时发现题目没有限定条件，说明解是一定存在的，考虑尝试证明这个东西。

显然解一定存在，考虑点 $u$ 直接连接了若干个点，对于 $v \to u$ 那么 $v$ 本质上可以看成一个星图。那么如果存在 $v \to u, y \to u$ 显然 $(v, y)$ 这条边无论方向是怎么样的都是可以归结于上面情况，所以当 $m = 0$ 的时候解一定存在。

尝试按照上述的方法进行构造，考虑先将 $1$ 作为根节点，之后考虑询问其他节点与 $1$ 连接的情况，如果存在边 $(1, u)$ 那么点 $u$ 本质没有贡献，如果存在边 $(u, 1)$ 考虑分类讨论：

- 如果 $1$ 已经有了一个继承节点 $v$ 询问 $(u, v)$ 之后进行合并。

- 如果没有，直接将 $1$ 合并到 $v$ 上。

这样对于每个节点每次最多询问 $1$ 次，总共应该是 $n - 1$ 次。

考虑 $m \ne 0$ 的情况，需要考虑当前节点是否是通过 $\tt \color{pink}\text{粉色}$ 边满足答案的。

假设**粉色**边构成的是若干条链怎么做，显然可能成为答案的只有是粉色边的顶端，而且如果选择了一条**粉色链**另外的所有点就是通过**绿色边**到达的。

首先考虑直接是链的情况怎么做，考虑从每条链最底部开始向上走。优先考虑自己链的情况，如果祖先的某个点 $u$ 与当前点 $v$ 有边 $u \to v$，那么我们将答案点换成 $u$ 是可行的。

之后考虑我们在链上已经确定了点，考虑对于其他的点是否可行。我们还是优先考虑其他链上的点如果不合法可以直接换掉，这样所有的链就确定完了，发现我们事实上走的全部是绿色的边，所以对于单点同理可得。

不考虑树了，我们直接考虑 $\tt DAG$，考虑按照拓扑排序分层，优先按照同层处理，证明同理。

对于图如何变成 $\tt DAG$ 呢？直接把他缩点即可，对于同一个强联通分量的点如果跳祖先的话需要直接跳而不是换一个强联通分量的点再重新来过，复杂度貌似是和之前分析同理是 $n - 1$ 的。

> 貌似对了，但是实现还需考虑。

### 正解

和我之前说的差不多，这里讲讲实现。

考虑对于 $m = 0$ 的部分处理单独设置类 `top`，对于 $\tt pink$ 边进行缩点之后按照拓扑排序加入节点，当某个时刻 `top` 的大小为 $1$，就结束了。

**注意**我们是要在每次处理结束选择废弃的节点进行更新。

然后对于一个强联通分量的情况需要特判，如果一个节点不合法就直接换上环的另外一个节点，需要保证在 `top` 内任意两点没有粉色边。

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
int n, m, head[maxn], cnt(1);
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}
int dfn[maxn], low[maxn], st[maxn], ed(0), dfntot(0), scctot(0), in[maxn], bl[maxn];
void tarjan(int p) {
    dfn[p] = low[p] = ++ dfntot, in[p] = 1, st[++ ed] = p;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        if(!dfn[to]) tarjan(to), low[p] = min(low[p], low[to]);
        else if(in[to]) low[p] = min(low[p], dfn[to]);
    }
    if(dfn[p] == low[p]) {
        int y; ++ scctot;
        do {
            y = st[ed --], in[y] = 0, bl[y] = scctot;
        } while(y != p);
    }
}

vector<pair<int, int>> E;
int deg[maxn];

void rebuild() {
    int i;
    for(i = 1; i <= n; ++ i) if(!dfn[i]) tarjan(i);
    cnt = 1; for(i = 1; i <= n; ++ i) head[i] = 0;
    for(const auto& v: E) if(bl[v.first] != bl[v.second]) {
        add(v.first, v.second), ++ deg[v.second];
    }
}

int q[maxn], hd, tl;

vector<int> lis[maxn];
int vis[maxn];

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v; r1(u, v), add(u, v), E.push_back({u, v});
    }
    rebuild();
    for(i = 1; i <= n; ++ i) if(deg[i] == 0) {
        if(vis[bl[i]]) lis[bl[i]].emplace_back(i);
        else vis[bl[i]] = 1, q[tl ++] = i;
    }
    while(tl - hd > 1) {
        int u = q[hd], v = q[hd + 1];
        hd += 2;
        printf("? %d %d\n", u, v);
        fflush(stdout);
        int x; r1(x); if(x == 0) swap(u, v);
        q[-- hd] = u, vis[bl[v]] = 0;
        for(i = head[v];i;i = edg[i].next) {
            int to = edg[i].to; -- deg[to];
            if(deg[to] == 0) {
                if(vis[bl[to]]) lis[bl[to]].emplace_back(to);
                else vis[bl[to]] = 1, q[tl ++] = to;
            }
        }
        if(lis[bl[v]].size() > 0) {
            vis[bl[v]] = 1, q[tl ++] = lis[bl[v]].back(), lis[bl[v]].pop_back();
        }
    }
    printf("! %d\n", q[hd]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```