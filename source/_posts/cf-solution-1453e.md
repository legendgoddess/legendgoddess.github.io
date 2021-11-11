---
title: CF1453E Dog Snacks
date: 2021-11-11 20:18:00
tags:
  - Dp
  - 贪心
categories:
  - CF题解
---

<h3><center>CF1453E Dog Snacks</center></h3>

> [CF1453E Dog Snacks](https://codeforces.ml/problemset/problem/1453/E)

可能一开始会简单地考虑将深度最大的作为 $k$，但是样例已经很显然得告诉可以通过跳到一个稍微浅一点的位置。

那么显然有一个很明显的贪心，就是我们设 $f(i)$ 表示子树 $i$ 的结尾叶子，也就是深度最浅的叶子节点和 $i$ 的距离。那么我们显然肯定最终是选择到一个深度最浅的点。

我们具体跳的过程肯定是优先跳深度大的，之后逐渐向小的跳跃。我们考虑这里不可避免的贡献，也就是如果是深度最大的需要跳到别的子树，就是其深度 $+1$。

我们特殊考虑一下根节点，也就是我们跳完时候还要回到根节点，那么我们可以考虑在根节点的时候最后到深度最大的子树，这样我们的贡献就**只有**最大深度了，显然更优秀。

---

我们总结一下：

- 不是根节点：
  - 不是一条链：$mxdep + 1$。
  - 是一条链：不更新，因为我们会在根节点的时候同一计算。
- 根节点：
  - 是一条链：直接就是最大深度。
  - 不是一条链：$\max($最大深度，次大深度 $+1)$。

---

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

const int maxn = 2e5 + 5;
int n;
struct Graph {
    vector<int> vc[maxn];
    void add(int u,int v) {
        vc[u].push_back(v);
    }
    void Clear() { for(int i = 1; i <= n; ++ i) vc[i].clear(); }
}G;

int ans(0);
int f[maxn];

void Solve() {
    int i, j;
    r1(n); ans = 0;
    for(i = 1; i <= n; ++ i) f[i] = 0x3f3f3f3f;
    for(i = 1; i < n; ++ i) {
        int u, v;
        r1(u, v), G.add(u, v), G.add(v, u);
    }
    function<void(int, int)> dfs;
    dfs = [&](int p,int pre) {
        int mx[2] = {0, 0};
        int sn(0);
        for(int v : G.vc[p]) if(v != pre) {
            dfs(v, p);
            ++ sn;
            f[p] = min(f[p], f[v] + 1);// 最浅的叶子
            if(f[v] + 1 > mx[0]) mx[1] = mx[0], mx[0] = f[v] + 1;
            else mx[1] = max(mx[1], f[v] + 1);
        }
        if(sn == 0) return f[p] = 0, void();
        if(p == 1) {
            if((int)G.vc[p].size() > 1) ans = max(ans, max(mx[0], mx[1] + 1));
            else ans = max(ans, mx[0]);
        }
        else if((int)G.vc[p].size() > 1) ans = max(ans, mx[0] + 1);
    };
    ans = 0;
    dfs(1, 0);
    printf("%d\n", ans);
    G.Clear();
}

signed main() {
    int T; r1(T); while(T --) Solve();
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }

```

