---
title: CF1550F Jumping Around
date: 2021-09-13 18:44:18
tags:
  - 贪心
  - 构造
categories:
  - CF题解
---


<h3> <center> CF1550F Jumping Around </center></h3> 

>  [CF1550F Jumping Around](https://www.luogu.com.cn/problem/CF1550F)

> 经典的转化，可能有点正难则反的意味。

---

@[toc]
#### $\textbf{我的思路：}$

可以看出因为 $d$ 是不变的，所以对于一个逐渐增大的 $k$ 可以到达的点是逐渐变多的，换言之就是具有**单调性**。

有一个比较显然的想法，就是离线一下 $k$ 考虑从小到大进行计算。考虑暴力，就是对于每一个 $k$ 建图跑一遍 $\textbf{Dijstra}$。 

复杂度是 $O(n^2 + q\ n\log n)$ 的，用一下线段树优化建图复杂度就是 $O(q\ n \log n)$ 了。

尝试考虑能否继承，但是没有结果。那么我们对于一个 $k$ 进行判断，不妨我们判断对于一条路径其合法需要的最小的 $k$。

----

#### $\textbf{题解：}$

我们思考对于一条路径其最小的 $k$ 就是其路径上的最大值，也就是说我们对于任意一条路径我们要让其最大值最小。这就可以使用最小生成树维护了 $(\text{也就是最小生成树的定义})$。

但是发现边数是 $O(n^2)$ 级别的，直接会想到那个奇妙的 $\textbf{Boruvka}$ 算法。

具体来说流程是这样的：

- 每次合并两个连通块
- 合并的时候对于一个连通块找到其连接的边权最小的边进行合并。
- 总共之后合并 $O(\log n)$ 次。

----

我们考虑如何找到最小的边，对于一个连通块我们需要找到和其相连的边。不妨我们设全集为 $U$，当前连通块的点集为 $I$。那么我们需要找到 $\min_{v \in I, u \in U/I} |d - |a_u - a_v||$。

对于 $u$ 我们使用 $\textbf{set}$ 进行维护，每次删除当前集合的所有点，之后对后面进行分类讨论即可，复杂度是 $O(n \log ^ 2 n)$ 的。

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

int n, Q, S, d;
int a[maxn];
set< pair<int, int> > s;

int head[maxn], cnt;
struct Edge {
    int to, next, w;
}edg[maxn << 1];
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) { v, head[u], w }, head[u] = cnt;
}

int mxk[maxn];

void dfs(int p,int pre,int mx) {
    mxk[p] = mx;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to, w = edg[i].w;
        if(to == pre) continue;
        dfs(to, p, max(mx, w));
    }
}

vector<int> vc[maxn];

/*
以 i 为中心的联通分量节点
*/

int fa[maxn];

int getfa(int x) {
    return x == fa[x] ? x : fa[x] = getfa(fa[x]);
}

void merge(int u,int v) {
    int s1 = getfa(u), s2 = getfa(v);
    if(s1 == s2) return ;
    fa[s1] = s2;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, Q, S, d);
    for(i = 1; i <= n; ++ i) r1(a[i]), s.insert(make_pair(a[i], i));
    for(i = 1; i <= n; ++ i) fa[i] = i;
    while(1) {
        for(i = 1; i <= n; ++ i) vc[i].clear();
        int flag(1);
        for(i = 1; i <= n; ++ i) {
            if(getfa(i) != getfa(1)) flag = 0;
            vc[getfa(i)].push_back(i);
        }
        if(flag) break;

        for(i = 1; i <= n; ++ i) if(vc[i].size()) {
            int x(0), y(0), w(2e9);

            for(int v : vc[i]) s.erase(make_pair(a[v], v));

            for(int v : vc[i]) {
                auto it = s.lower_bound(make_pair(a[v] + d, 0));
                int tmp;
                if(it != s.end()) {
                    tmp = abs(d - abs(a[v] - it->first));
                    if(tmp < w) x = v, y = it->second, w = tmp;
                }
                if(it != s.begin()) {
                    -- it;
                    tmp = abs(d - abs(a[v] - it->first));
                    if(tmp < w) x = v, y = it->second, w = tmp;
                }

                it = s.lower_bound(make_pair(a[v] - d, 0));

                if(it != s.end()) {
                    tmp = abs(d - abs(a[v] - it->first));
                    if(tmp < w) x = v, y = it->second, w = tmp;
                }
                if(it != s.begin()) {
                    -- it;
                    tmp = abs(d - abs(a[v] - it->first));
                    if(tmp < w) x = v, y = it->second, w = tmp;
                }

            }
            for(int v : vc[i]) s.insert(make_pair(a[v], v));
            if(getfa(x) != getfa(y)) {
                add(x, y, w), add(y, x, w);
//                printf("%d %d %d\n", x, y, w);
                merge(x, y);
            }
        }

    }

    dfs(S, 0, 0);
    while(Q --) {
        int pos, K;
        r1(pos, K);
        if(mxk[pos] <= K) puts("YES");
        else puts("NO");
    }
	return 0;
}

```





