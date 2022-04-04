---
title: "CF1477D Nezzar and Hidden Permutations 题解"
date: 2022-4-4 9:19:00
tags:
  - 图论
categories:
  - CF题解
---

[Problem - 1477D - Codeforces](https://codeforces.com/problemset/problem/1477/D)

**题目描述：**

给定一张 $n$ 个点 $m$ 条边的简单无向图，构造两个排列 $p,q$，使得：

- 对任意 $(u,v) \in E，(p_u​−p_v​)(q_u​−q_v​)>0$。
- 在此基础上，最大化 $p_i \ne q_i$ 的个数。 

$1\le n,m \le 5×10^5$。

---

考虑上述限制的本质，如果说已经确定了排列 $p$，那么对于 $p_u, p_v$，$(u, v)$ 在 $q$ 中的相对位置也是确定的。所以本质上就是给边定向，在这之后求出拓扑排序的两个排列使得对应位置不同的元素个数最大。

观察样例发现大部分情况下都是可以构造全部错位的情况，显然如果一个点同时被所有点限制才能确定位置，也就是度数为 $n - 1$。所以这样的点其实本质上不需要考虑，考虑删除点之后对于每个连通块进行计算。

考虑之后的图是怎么样的，就是所有点的度数至多为 $n - 2$，但是感觉一下子没有什么用。

考虑直接对于连通块进行 $\tt bfs$ 分层，同一层错位排序发现如果存在一层只有 $1$ 个点就去世了，所以我们考虑一下星图的情况。对于星图貌似已经不行了。

我们尝试一下补图，这样每个点的度数至少为 $1$ 所以每个连通块的大小至少为 $2$。

考虑补图的星图情况，本质上就是确定了原图中若干连通分量的先后顺序，这样我们显然可以直接暴力进行钦定也是没有问题的。

不妨钦定 $p = 1, 2, \cdots , n, q = n, 1, 2, \cdots, n - 1$。因为原图中根节点本质上是没有连边得到，所以只要保证剩下节点顺序相同即可。

那么我们只要将补图做完不就行了吗？

考虑对于补图可以拆成**若干个星图**，每个星图的大小都是 $\ge 2$ 的。

如何进行剖分？

- 首先如果一个点的周围点都没有处理，直接成为一个星图。

- 如果周围存在点被处理了，显然这个点**不是根**，考虑我们不能直接使用点 $u$ 作为根。
  
  - 如果 $siz_v = 2$ 考虑直接钦定 $v$ 作为根节点，之后连接 $u$。
  
  - 如果 $siz_v > 2$ 考虑拆掉 $v$，让 $u$ 进行操作 $1$。

可以发现肯定是可以剖分成一个合法的情况的，考虑对于星图之间的顺序怎么处理。

我们参考之前对于单个星图的处理，星图之间本质上是不用管的，对于任意两个星图总有一个星图的所有点的位置是比另外一个大的。

对于这个东西我们还是考虑找到一个 $\tt dfs$ 生成树来计算，使用 $\tt set$ 维护补图。

具体来说就是考虑遍历所有的当前存在点集 $S$，对于一个点 $v$ 不在边上就直接加边之后删除当前节点。

可以发现对于当前的点 $p$ 考虑的补图的边都是没有加入到补图的边，而且一个点被访问的次数最多为边数 $m$ 次，所以复杂度是正确的。

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

const int maxn = 5e5 + 5;
int n, m;
set<int> vc[maxn], S;
vector<int> tr[maxn], buc[maxn];
int bl[maxn], siz[maxn], rt[maxn], deg[maxn];
int p[maxn], q[maxn], ctot(0);

void dfs1(int p,int pre) {
    for(const auto& v: S) if(!vc[p].count(v)) tr[p].emplace_back(v), tr[v].emplace_back(p);
    for(const auto& v: tr[p]) if(v != pre) S.erase(v);
    for(const auto& v: tr[p]) if(v != pre) dfs1(v, p);
}

void dfs2(int p,int pre) {
    int fl(0);
    if(!bl[p]) {
        for(const auto& v: tr[p]) if(!bl[v]) fl = 1;
        if(fl) {
            bl[p] = ++ ctot, siz[ctot] = 1, rt[bl[p]] = p;
//            printf("p = %d, siz = %d\n", p, ctot);
            for(const auto& v: tr[p]) if(!bl[v]) bl[v] = ctot, ++ siz[ctot];
//            for(int i = 1; i <= n; ++ i) przintf("%d : %d\n", i, bl[i]);
        }
        else {
            for(const auto& v: tr[p]) if(bl[v]) {
                if(rt[bl[v]] == v) bl[p] = bl[v], ++ siz[bl[v]];
                else if(siz[bl[v]] == 2) { rt[bl[v]] = v, bl[p] = bl[v], ++ siz[bl[v]]; }
                else if(siz[bl[v]] > 2) { -- siz[bl[v]], bl[p] = bl[v] = ++ ctot; siz[ctot] = 2, rt[ctot] = p; }
                break;
            }
        }
    }
    for(const auto& v: tr[p]) if(v != pre) dfs2(v, p);
}

void Solve() {
    int i, j;
    static queue<int> qu; while(!qu.empty()) qu.pop();
    S.clear();
    ctot = 0;
    r1(n, m);
    for(i = 1; i <= n; ++ i) buc[i].clear(), tr[i].clear(), vc[i].clear(), S.insert(i);
    for(i = 1; i <= n; ++ i) bl[i] = siz[i] = rt[i] = deg[i] = p[i] = q[i] = 0;
    for(i = 1; i <= m; ++ i) {
        int u, v; r1(u, v), vc[u].insert(v), vc[v].insert(u);
        ++ deg[u], ++ deg[v];
    }
    int nowtot(n);
    for(i = 1; i <= n; ++ i) if(deg[i] == n - 1) qu.emplace(i), S.erase(i);
    while(!qu.empty()) {
        int u = qu.front(); qu.pop();
        p[u] = q[u] = nowtot --;
        for(const auto& v: vc[u]) {
            -- deg[v];
            if(deg[v] >= nowtot - 1 && S.count(v)) S.erase(v), qu.emplace(v);
        }
    }
    for(i = 1; i <= n; ++ i) if(S.count(i)) S.erase(i), dfs1(i, 0);
    for(i = 1; i <= n; ++ i) if(!p[i] && !bl[i]) dfs2(i, 0);
    for(i = 1; i <= n; ++ i) if(bl[i] > 0 && rt[bl[i]] != i) buc[bl[i]].emplace_back(i);
//    for(i = 1; i <= n; ++ i) {
//        printf("%d : ",i);
//        for(const auto& v: tr[i]) printf("%d ", v);
//        puts("");
//    }
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, bl[i]);
    for(int _ = 1, s = 1; _ <= ctot; ++ _, ++ s) {
        p[rt[_]] = s;
        for(const int& v: buc[_]) {
            q[v] = s, p[v] = ++ s;
        }
        q[rt[_]] = s;
    }
    for(i = 1; i <= n; ++ i) printf("%d%c", p[i], " \n"[i == n]);
    for(i = 1; i <= n; ++ i) printf("%d%c", q[i], " \n"[i == n]);
}

/*
1
6 6
1 2
2 3
3 4
3 5
3 6
3 1
*/

signed main() {
	int i, j, T;
    r1(T); while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```






