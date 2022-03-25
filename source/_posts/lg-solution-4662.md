---
title: "P4662 [BalticOI 2008]黑手党 题解"
data: 2022-3-24 21:09:00
tags:
  - 最小割
  - 图论
categories:
  - 洛谷题解
---

[[BalticOI 2008]黑手党 - 洛谷](https://www.luogu.com.cn/problem/P4662)

发现就是考虑对多少个点打标记使得 $S \to T$ 的任意一条路径都是有标记的点。

本质就是割掉若干个点使得 $S,T$ 不连通。 

每个点有权值也是没有关系的，所以直接拆点跑最小割就行了。

**注意：** 如果要输出方案的话，我们考虑遍历 $S$ 可以到达的点集，对于每个点打标记，如果说存在一条边使得两边的点是在不同的集合，那么这条边就是被割掉的。

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

const int maxn = 2e5 + 5;
int n, m, S, T;
int head[maxn], cnt(1), head1[maxn];
struct Edge {
    int u, to, next, w;
}edg[maxn << 1];

void add1(int u,int v,int w) {
    edg[++ cnt] = (Edge) {u, v, head[u], w}, head[u] = cnt;
}

void add(int u,int v,int w) {
    add1(u, v, w), add1(v, u, 0);
}

int d[maxn], vis[maxn];
int bfs() {
    for(int i = 1; i <= 2 * n; ++ i) d[i] = -1;
    static queue<int> q; while(!q.empty()) q.pop();
    q.push(S), d[S] = 0;
    while(!q.empty()) {
        int u = q.front(); q.pop();
        if(u == T) return 1;
        for(int i = head[u];i;i = edg[i].next) {
            int to = edg[i].to, w = edg[i].w;
            if(d[to] == - 1 && w) q.push(to), d[to] = d[u] + 1;
        }
    }
    return d[T] != -1;
}

int dfs(int p,int sum) {
    if(p == T) return sum;
    int res(sum);
    for(int &i = head1[p];i;i = edg[i].next) {
        int to = edg[i].to, w = edg[i].w;
        if(w > 0 && d[to] == d[p] + 1) {
            int s = dfs(to, min(res, w));
            if(s) edg[i].w -= s, edg[i ^ 1].w += s, res -= s;
            if(!res) return sum;
        }
    }
    if(res == sum) d[p] = 0;
    return sum - res;
}
const int inf = 0x3f3f3f3f;
int mxflow() {
    int ans(0);
    while(bfs()) {
        for(int i = 1; i <= 2 * n; ++ i) head1[i] = head[i];
        ans += dfs(S, inf);
    }
    return ans;
}

void dfs(int p) {
    vis[p] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        if(edg[i].w > 0 && !vis[to]) dfs(to);
    }
}

signed main() {
    int i, j;
    r1(n, m, S, T);
    T += n;
    for(i = 1; i <= n; ++ i) {
        int c; r1(c);
        add(i, i + n, c);
    }
    for(i = 1; i <= m; ++ i) {
        int x, y;
        r1(x, y);
        add(x + n, y, inf), add(y + n, x, inf);
    }
    int ans = mxflow();
    vector<int> vc;
    dfs(S);
    for(i = 2; i <= cnt; i += 2) {
        int v = edg[i].to, u = edg[i ^ 1].to;
        if(vis[u] && !vis[v]) vc.emplace_back(u);
    }
    sort(vc.begin(), vc.end());
    for(const int& v : vc) printf("%d ", v);
    puts("");
//    printf("%d\n", ans);
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }//
```
