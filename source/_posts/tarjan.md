---
title: Tarjan 缩点, 强联通分量
date: 2021-10-19 15:40:48
tags:
  - 图论
categories:
  - 算法
---
<h3><center>Tarjan 缩点，强联通分量</center></h3>

@[toc]
### 引入

如果说需要对于一个图上进行 $\tt Dp$，但是同一个环上的点无法进行转移怎么办？

我们只能做 $\tt DAG$ 上的 $\tt Dp$，但是有环我们该怎么办？

缩环！缩点！求强联通！

> 当然我不是说隔壁的带花树。

------

> 虽然说所有的代码都是我随手打的，但是都已经经过测试，请放心阅读。

### 代码基础定义

- `dfn[x]`表示当前点遍历顺序的编号。
- `low[x]`表示当前点通过其 $\tt dfs$ 的子树以及能通过一条边到达的点，最浅能到达的编号。

### 强联通分量

#### 定义：

&emsp; 有向图中能任意到达的极大点集，被称为一个强联通分量。

&emsp; 对于一张图，我们将其划分成若干个这样的极大点集，每个点集之间的连边关系构成一张 $\color{red}\tt DAG$。   

#### 代码实现：

&emsp; 我们遍历每一个弱联通分量，考虑使用一个栈来维护每个点的进入顺序。

&emsp; 每次通过其能到达的点来更新 `low[p]`。

> - 如果说当前的儿子没有被遍历过，先遍历然后通过 `low`更新。
> - 如果说当前的儿子在栈中，直接通过 `dfn`更新就好了。

&emsp; 这个更新就是根据定义来的。

&emsp; 那么什么时候可以统计一个强联通分量呢？当 `dfn[p] = low[p]`的时候，也就是意味着其是该强联通分量 $\tt dfs$ 时候遍历的第一个点，这样可以保证剩下的点都在栈中，我们直接退栈即可。

```cpp
void tarjan(int p) {
    dfn[p] = low[p] = ++ tot, vis[p] = 1, st[++ ed] = p;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        if(!dfn[to]) tarjan(to), low[p] = min(low[p], low[to]);
        else if(vis[to]) low[p] = min(low[p], dfn[to]);
    }
    if(dfn[p] == low[p]) {
        int y; bel[p] = ++ Sz, vis[p] = 0;
        while((y = st[ed --]) != p) {
            bel[y] = Sz, vis[y] = 0;
        }
    }
}
```

------

### 边双联通分量和桥

> 注意是在无向联通图中的。

#### 定义：

&emsp; 桥：割掉当前的边，使得图不连通。

&emsp; 边双联通分量：一个点集，使得任意两点之间有两条本质不同的路径，本质不同指路径上没有相同的边。

#### 代码实现：

&emsp; 和之前的强联通分量相似。我们考虑先计算出所有桥，之后直接遍历连通块即可。

&emsp; 和之前不同的在于，我们需要考虑父亲节点的贡献，我们考虑一条边 $u \to v$ 是桥意味着 ```low[v] > dfn[u]```。

&emsp; 具体含义表示其最浅$\color{red}\text{不能}$到达点 $u$，那么割掉 $u \to v$ 的边就不能联通了。

&emsp; 正是因为这个定义所以需要考虑$\color{red}\text{父亲节点}$，对于父亲节点我们不能让其产生贡献。

```cpp
int brg[maxn << 1]; // 表示当前边是否为桥
void tarjan(int p,int pre) {
    dfn[p] = low[p] = ++ tot;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        if(!dfn[to]) {
            tarjan(to, i);
            low[p] = min(low[p], low[to]);
            if(dfn[p] < low[to]) brg[i] = brg[i ^ 1] = 1;
        }
        else if(i != (pre ^ 1)) low[p] = min(low[p], dfn[to]);
    }
}
/*
注意前向星建边，开始值是 cnt = 1，每次 ++ cnt。
*/
```

&emsp; 如果说需要求边双，我们直接对于每一个点进行搜索，不走桥边即可。

> 本质上就是将桥都断开，看联通的极大点集。

------

### 点双联通分量和割点

> 注意是在无向联通图中的。

#### 定义：

&emsp; 割点：删除了该点，使得图不连通。

&emsp; 点双联通分量：点集中任意两点间，存在两条本质不同路径，本质不同指不经过相同的点和边（除了这两个点）。

#### 代码实现

&emsp; 回忆一下我们边双的求法，判定条件是 `dfn[p] < low[to]`，但是我们割点不同。

&emsp; 即使儿子能到达当前点，但是我们割掉当前点之后还是不连通，所以是 `dfn[p] <= low[to]`。

&emsp; 可以发现这时我们就不需要考虑父亲节点是否产生贡献了。

&emsp; 我们可以直接在条件合法的时候将儿子节点的 $\tt dfs$ 树上的节点都加入，同时加入当前节点 $u$。

&emsp; $\Large\color{red}\text{注意：}$

&emsp; 同一个点可能属于多个点双联通分量，所以我们最终建立点双图的时候需要再注意。

&emsp; 我们需要特判，每个开始遍历的节点，必须遍历两个儿子之后才可能称为割点，但是点双是不影响的。

&emsp; 单个点也是一个 $\tt dcc$。

&emsp; 我们考虑把割点都单独建立成新的点，之后遍历每一个点双，将其和其子集中的割点连边即可。

```cpp
int Rt; // Rt 表示遍历的开始节点
void tarjan(int p) {
    dfn[p] = low[p] = ++ tot, st[++ ed] = p;
    if(!head[p] && rt == p) return dcc[++ Sz].push_back(p), void();
    int fl(0);
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        if(!dfn[to]) {
            ++ fl;
            tarjan(to);
            low[p] = min(low[p], low[to]);
            if(dfn[p] <= low[to]) {
                if(fl > 1 || p != rt) cut[p] = 1;
                ++ Sz; int y;
                do {
                    y = st[ed --];
                    dcc[Sz].push_back(y);
                }while(y != to);
                dcc[Sz].push_back(p);
            }
        }
        else low[p] = min(low[p], dfn[to]);
    }
}
```

&emsp; 下面是重新建图。

```cpp
int tSz(Sz);
for(i = 1; i <= n; ++ i) if(cut[i]) nid[i] = ++ tSz, bel[i] = tSz;
for(i = 1; i <= Sz; ++ i) {
    for(int v : dcc[i]) if(cut[v]) {
        Add(i, nid[v]), Add(nid[v], i);
    }
    else bel[v] = i;
}
```


