---
title: CF1632E2 题解
date: 2022-03-07 9:38:00
tags:
  - 图论
categories:
  - CF题解
---

[Problem - 1632E2 - Codeforces](https://codeforces.com/problemset/problem/1632/E2)

首先有一个很直观的解法，发现对于连边肯定是贪心去连**最长链**到根节点的一条边，之后通过两遍 $\tt dfs$ 来找到答案，复杂度 $O(n)$。

但是上述的解法有很明显的问题：

- 如何选择最优的最长链？

- 为什么不可以连接到两个点的**中间**？

我们着重按照这个问题来思考解法。

既然已经知道了肯定是连接根节点的边，我们考虑对于两个深度都大于当前答案 $\tt Ans$ 的点，我们如何才能找到最优的解，设其距离为 $\tt dis$。我们显然可以让两个点的 $d$ 变成 $\tt \lceil\frac{d}{2}\rceil + L$。

> 就是在路径的中间的某个点连接根节点。

对于所有的这样的点我们都需要考虑一遍吗？

其实我们只要考虑两棵子树中深度最大的点构成的距离即可。

> 事实上到这里这个题目就做完了，还有一些细节需要完善，已经明白的大佬可以直接去看[代码](#Code)。

对于这样的两个点，显然深度小的点如果不需要考虑那么对于深度大的点我们可以**直接**进行连边，所以这个点对生效**当且仅当** $\tt Ans$ **小于**深度较小的点。

我们显然不需要考虑根节点，但是同一个子树内的两个点需要考虑吗？

当出现这种情况肯定是正好是一条链的时候，这种情况是需要考虑进去的。

> 也就是祖先和深度最大的节点同时不满足条件，我们可以通过向中间的节点靠拢之后走特殊边。

答案显然是单调递增的。

<div id = "Code"><div>

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
    namespace Read {
//        #define Fread
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

const int maxn = 3e5 + 5;

int n, m;
int head[maxn], cnt(1);
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}
int mx[maxn], dep[maxn], mxd[maxn][2];

void dfs(int p,int pre) {
    if(p != 1) dep[p] = dep[pre] + 1;
    mxd[p][0] = mxd[p][1] = dep[p];
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        dfs(to, p);
        if(mxd[to][0] > mxd[p][0]) mxd[p][1] = mxd[p][0], mxd[p][0] = mxd[to][0];
        else mxd[p][1] = max(mxd[p][1], mxd[to][0]);
    }
    int dis = mxd[p][0] + mxd[p][1] - 2 * dep[p];
    if(mxd[p][1] != 0) mx[mxd[p][1]] = max(mx[mxd[p][1]], dis);
}

void Solve() {
    int i, j;
    r1(n);
    for(i = 1; i < n; ++ i) {
        int u, v; r1(u, v);
        add(u, v), add(v, u);
    }
    dfs(1, 0);
//    for(i = 0; i <= n; ++ i) printf("%d : %d\n", i, mx[i]);
    int ans(0), up = mxd[1][0];
    for(i = up - 1; i >= 0; -- i) mx[i] = max(mx[i], mx[i + 1]);
    for(i = 1; i <= n; ++ i) {
        while(ans < up && (mx[ans + 1] + 1) / 2 + i > ans) ++ ans;
        printf("%d%c", ans, " \n"[i == n]);
    }
    for(i = 1; i <= n; ++ i) mx[i] = head[i] = dep[i] = mxd[i][0] = mxd[i][1] = 0;
    cnt = 1;
}

signed main() {
    int i, j, T;
    r1(T); while(T --) Solve();
    return 0;
}
/*
1
7
1 2
1 3
3 4
3 5
3 6
5 7
*/

}


signed main() { return Legendgod::main(), 0; }
```
