---
title: 虚树浅谈
date: 2021-11-19 15:24:59
tags:
  - 数据结构
categories:
  - 算法 
---

<h3><center>虚树浅谈</center></h3>

>  我们就直接进入主题了，毕竟还有 $1 \tt h$ 就要准备去考场了，可能是我退役前的最后一篇学术类的吧。

---

树上的题目中可能会给出 $\sum k_i \le 5 \times 10^5$ 之类的限制，那么我们常常会考虑到两种东西：

- 自然根号，也就是本质不同的 $k_i$ 只有 $\sqrt V$ 种。

- 虚树。

对于给定的一个点集，我们往往不希望这个是一个森林，所以我们钦定加上根节点特判即可。

我们考虑将点集按照 $\tt dfs$ 序进行排序，之后从前向后往栈中加点。我们设 $lc$ 表示当前点和最后加入栈的点的 $\tt Lca$。

- 如果 $lc = stack(ed)$ 那么意味着还是同样一个子树的链，我们直接加入即可。

- 不然意味着更换了子树，我们考虑将当前子树的树全部建出，那么因为更换了子树，也就是意味着 $dfn_{lc} \le stack(ed)$ 的，我们考虑退栈直到合法为止。

  ```cpp
  while(ed > 1 && dfn[lc] <= dfn[st[ed - 1]]) G.add(st[ed - 1], st[ed]), -- ed;
  ```

  我们栈中还有最后一个点没有退栈，那么意味着 `st[ed - 1]` 已经是 `lc` 的祖先了，而且 `dfn[lc] <= dfn[st[ed]]` 也就是意味着要么两者相同，要么 `lc` 是 `st[ed]` 的祖先，加边即可。

  之后再加上当前的节点。

具体来说实现是这样的：

```cpp
    st[ed = 1] = 1;
    for(int i = 1; i <= len; ++ i) {
        int lc = Lca(buc[i], st[ed]);
        if(lc == st[ed]) { st[++ ed] = buc[i]; continue; }
        while(ed > 1 && dfn[lc] <= dfn[st[ed - 1]]) G.add(st[ed - 1], st[ed]), -- ed;
        if(lc != st[ed]) {
            G.add(lc, st[ed]);
            st[ed] = lc;
        }
        st[++ ed] = buc[i];
    }
    while(ed > 1) G.add(st[ed - 1], st[ed]), -- ed;
```

----

其实还有一种建树的方法，我们考虑建立欧拉序设点 $p$，进入和出去的标号分别是 `dfin[p], dfot[p]`。

我们可以将需要的 $\tt Lca$ 拿出来，之后复制一份作为 `dfot[p]` 然后排序，用栈来维护即可。来自 `shadowice1984` 的题解。

```cpp
    int cmp(const int &a, const int &b) {
        int x = a < 0 ? dfot[- a] : dfin[a];
        int y = b < 0 ? dfot[- b] : dfin[b];
        return x < y;
    }

    buc[++ len] = 1;
    sort(buc + 1, buc + len + 1, cmp);
    for(int i = 1; i <= len; ++ i) vvis[buc[i]] = 1;
    for(int i = 1, j = len; i < j; ++ i) {
        int lc = Lca(buc[i], buc[i + 1]);
        if(!vvis[lc]) buc[++ len] = lc, vvis[lc] = 1;
    }
    for(int i = 1, j = len; i <= j; ++ i) buc[++ len] = - buc[i], vvis[buc[i]] = 0;// Clear
    sort(buc + 1, buc + len + 1, cmp);
    ed = 0;
    for(int i = 1; i <= len; ++ i) {
        if(buc[i] > 0) st[++ ed] = buc[i];
        else {
            int v = st[ed], p = st[ed - 1]; -- ed;
            if(!ed) continue;
            ...
        }
    }
```

---

例题：

[P2495 [SDOI2011]消耗战](https://www.luogu.com.cn/problem/P2495)

[P4220 [WC2018]通道](https://www.luogu.com.cn/problem/P4220)

[P4565 [CTSC2018]暴力写挂](https://www.luogu.com.cn/problem/P4565)

> 要相信 $\tt sort$ 的速度。
