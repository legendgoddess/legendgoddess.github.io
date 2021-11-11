---
title: CF1494D Dogeforces
date: 2021-11-11 18:46:00
tags: 
  - 图论
  - 最小生成树
categories:
  - CF题解
---

<h3><center>CF1494D Dogeforces</center></h3>

> [CF1494D Dogeforces](https://codeforces.ml/problemset/problem/1494/D)

发现我们最终维护出来的树肯定是一个大根堆的样子，也就是父亲节点的权值严格大于子树的任意节点的权值，那么既然所有叶子节点的关系边权都已经给出了，我们不妨考虑使用 $\tt Kruscal$ 重构树进行构造。

但是重构树可能存在一种情况，也就是父亲节点的权值和儿子节点的权值是相同的，我们不妨考虑将这两个点缩成同一个点。

我们考虑这种构造是否合法，也就是是否仍然满足二叉树的性质，如果当前的点和父亲节点缩到一起的话，可以保证每个节点至少有两个儿子，正好和题目的要求相契合。

> 显然如果说恰好只有 $2$ 个儿子的话显然不能用构造，考虑树：
>
> ![I0mTmR.png](https://z3.ax1x.com/2021/11/11/I0mTmR.png)

```cpp
#include <bits/stdc++.h>
using namespace std;

namespace Legendgod {

namespace Read {
//    #define Fread
    #ifdef Fread
    const int Siz = (1 << 21) + 5;
    char buf[Siz], *iS, *iT;
    #define gc() (iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iT == iS ? EOF : *iS ++ ) : *iS ++)
    #define getchar gc
    #endif // Fread

    template <typename T>
    void r1(T &x) {
        x = 0; int f(1); char c(getchar());
        for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
        for(; isdigit(c); c = getchar()) x = (x * 10) + (c ^ 48);
        x *= f;
    }
    template <typename T, typename...Args>
    void r1(T &x, Args&...arg) {
        r1(x), r1(arg...);
    }

}

using namespace Read;

const int maxn = 5e2 + 5;

int n;
int a[maxn][maxn];
int val[maxn << 1], fa[maxn << 1];

vector<int> sn[maxn << 1];
int Val[maxn << 1], bel[maxn << 1];

signed main() {
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) for(j = 1; j <= n; ++ j) r1(a[i][j]);
    struct Node {
        int u, v, w;
        int operator < (const Node &z) const {
            return w < z.w;
        }
    };
    vector<Node> vc;
    for(i = 1; i <= n; ++ i) for(j = i + 1; j <= n; ++ j) vc.push_back((Node) {i, j, a[i][j]});
    sort(vc.begin(), vc.end());
    for(i = 1; i <= n; ++ i) val[i] = a[i][i];
    for(i = 1; i <= n; ++ i) Val[i] = val[i], bel[i] = i;
    for(i = 1; i <= (n << 1); ++ i) fa[i] = i;
    int tot(n);
    for(auto v : vc) {
        function<int(int)> getfa;
        function<void(int, int)> merge;
        getfa = [&](int x) { return x == fa[x] ? x : fa[x] = getfa(fa[x]); };
        merge = [&](int a,int b) {
            a = getfa(a), b = getfa(b);
            fa[a] = b;
        };
        int x = v.u, y = v.v, w = v.w;
        if(getfa(x) != getfa(y)) {
            val[++ tot] = w;
            sn[tot].push_back(getfa(x)), sn[tot].push_back(getfa(y));
            merge(x, tot), merge(y, tot);
        }
    }
//    printf("tot = %d\n", tot);
    function<void(int, int)> dfs;
    int totsz(n);
    vector<pair<int, int>> ans;
    dfs = [&](int p,int pre) {
//        printf("p = %d\n", p);
        if(p <= n) return ;
        if(val[p] != val[pre]) bel[p] = ++ totsz, Val[totsz] = val[p];
        else bel[p] = bel[pre];
        for(auto v : sn[p]) dfs(v, p);
        for(auto v : sn[p]) {
            if(bel[p] != bel[v]) ans.push_back({bel[v], bel[p]});
        }
    };
    dfs(tot, 0);
    printf("%d\n", totsz);
    for(i = 1; i <= totsz; ++ i) printf("%d%c", Val[i], " \n"[i == totsz]);
    printf("%d\n", bel[tot]);
//    printf("%d\n", totsz - 1);
    for(auto v : ans) printf("%d %d\n", v.first, v.second);
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }

```

