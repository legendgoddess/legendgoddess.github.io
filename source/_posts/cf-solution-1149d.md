---
title: "CF1149D Abandoning Roads 题解"
date: 2022-4-5 08:02:00
tags:
  - 图论
  - Dp
categories:
  - CF题解
---

[Problem - 1149D - Codeforces](https://codeforces.com/problemset/problem/1149/D)

**一点小思路**：

> 貌似有点问题。

首先保证图连通，发现点数为 $70$ 边数最多为 $200$。对于两种边权 $a, b$ 有一种想法就是让最短路包含在最小生成树中。或者说我们优先考虑边权小的边，先让其尽量成为生成树，能否直接暴力枚举端点 $i$，考虑暴力构建树。

是否有一个结论，就是我们考虑只使用 $a$ 边这样最短路肯定是可以在生成树上的。

如果仅仅使用 $a$ 边发现图是不连通的，我们考虑当前点 $i$ 所在的连通块。

现在本质上就是尝试暴力加入若干边，能否考虑使用 $b$ 边继续更新结果？

显然 $b$ 边只要能使图连通就是最小生成树了，考虑使用 $b$ 边使得 $1 \to i$ 的距离最短。

不妨考虑对于 $i$ 也进行一次最短路，是不是直接弗洛伊德更新呀？

就是每个点对之间打标记是否是通过了 $b$ 边进行更新即可。

---

### $\tt solution$

考虑还是对于最小生成树，先考虑 $a$ 边构成的若干个连通块，之后只要使用 $b$ 边保证距离最小就行了，考虑需要保证是树的情况，所以一个点出去了之后就不会再回来，直接使用状压 $f(S, u)$ 表示已经走过的点集是 $S$，从 $1$ 到节点 $u$ 的最小距离，复杂度是 $O(2^n (n + m) \log (2^n(n + m)))$ 不是很行。

注意我们考虑的是一棵树，并且需要保证最短路径，考虑最短路的时候什么东西是可以省掉的，考虑状态 $S$ 表示点集，那么是否存在一些点集本身不会被额外更新？

发现如果一条 $b$ 边连接了相同连通块中的两个点，这条边本质上是不合法的，直接判掉就行了。还是相同的情况，如果有两条 $b$ 边通过一个中间点到达呢？发现 $2a < 2b$ 对于连通块大小小于 $4$ 的部分是不可能被更新到的。那么这样的连通块就不需要记录了。

所以我们只需要记录 $O(2^\frac{n}{4}n)$ 的状态就行了，考虑如何更新最短路。

参考 $\tt bfs$ 的操作，但是因为只有两种边所以一个点最多被更新到两次，考虑开两个队列存两种边进行更新即可。

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
int n, m, A, B;
int head[maxn], cnt(1);
struct Edge {
    int to, next, w;
}edg[maxn << 1];

void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}

int bl[maxn], stot(0), siz[maxn], nstot(0), fl[maxn];

void dfs(int p,int c) {
    bl[p] = c, ++ siz[c];
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to, w = edg[i].w;
        if(w == A && !bl[to]) dfs(to, c);
    }
}

const int mxn = 70 + 5;
int f[(1 << 18) + 5][mxn];

queue< pair<int, int> > q[2];

int pd(const pair<int, int>& x, const pair<int, int>& y) {
    if(f[x.first][x.second] < f[y.first][y.second]) return true;
    else return false;
}

const int inf = 1e9;

signed main() {
	int i, j;
    r1(n, m, A, B);
    for(i = 1; i <= m; ++ i) {
        int u, v, w;
        r1(u, v, w), add(u, v, w), add(v, u, w);
    }
    for(i = 1; i <= n; ++ i) if(!bl[i]) {
        ++ stot; dfs(i, stot);
        fl[stot] = (siz[stot] > 3) ? ++ nstot : 0;
    }
    for(i = 1; i <= n; ++ i) for(j = 0; j < (1 << nstot); ++ j) f[j][i] = inf;
    f[0][1] = 0;
    q[0].push({0, 1}), q[1].push({0, 1});
    while(!q[0].empty() || !q[1].empty()) {
        pair<int, int> tmp;
        if(q[1].empty() || (!q[0].empty() && pd(q[0].front(), q[1].front()))) tmp = q[0].front(), q[0].pop();
        else tmp = q[1].front(), q[1].pop();
        int S = tmp.first, u = tmp.second;
        for(i = head[u];i;i = edg[i].next) {
            int to = edg[i].to, w = edg[i].w;
            if((bl[to] == bl[u] && w == B) || (fl[bl[to]] && ((S >> (fl[bl[to]] - 1)) & 1))) continue;
            int nxt = (bl[to] == bl[u] || !fl[bl[u]]) ? S : (S | (1 << (fl[bl[u]] - 1)));
            if(f[nxt][to] > f[S][u] + w) {
//            printf("(%d, %d) = %d, S = %d, nxt = %d, vl = %d\n", u, to, w, S, nxt, f[nxt][to]);
                f[nxt][to] = f[S][u] + w;
                if(w == A) q[0].push({nxt, to});
                else q[1].push({nxt, to});
            }
        }
    }
    int z = (1 << nstot);
    for(i = 1; i <= n; ++ i) {
        int res(0x3f3f3f3f);
        for(j = 0; j < z; ++ j) res = min(res, f[j][i]);
        printf("%d%c", res, " \n"[i == n]);
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
