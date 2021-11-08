---
title: Kruscal 重构树浅谈
date: 2021-10-09 19:56:52
tags:
  - 数据结构
  - 最小生成树
categories:
  - 算法
---

<h3><center>Kruscal 重构树浅谈</center></h3>

这个算法本质上就是通过 $\tt Kruscal$ 重构树的过程将边权变成点权之后建立一个堆。

具体来说就是每次选择一条合法的边，将边权变成点权之后连接原来边两边的节点。

---

这个是生成树上的性质，有些大佬说可以类比成笛卡尔树，不过那个是序列上的满足堆和 $\tt Bst$ 的性质的树。

> 可能性质还笛卡尔树更多一点。

----

**常用解法**

- 跳父亲找到符合条件的最小点。
- 维护 $\tt dfs$ 序判断点是否在内。
- 结合动态规划和数据结构维护书上信息等。

----

**常用的一个写法**

这种写法是特别针对将一条边的边权定做和相邻点的 $\tt min, max$ 相关的快速写法。

举个例子：维护 $\tt min$ 的最大生成树。

考虑加边的时候肯定是从权值最大的边开始加，我们考虑从大到小枚举点，钦定当前点代表当前权值的边。也就是另外一个点是比其大的。然后能加边就加边。

```cpp
    for(i = 1; i <= n; ++ i) fa[i] = i;
    for(i = n; i >= 1; -- i) {
        for(int j = head[i];j;j = edg[j].next) {
            int to = edg[j].to; if(getfa(to) > i) {
                add(vc1, i, getfa(to));
                merge(to, i);
            }
        }
    }
```

> 来自学姐 $\tt -$$\tt\color{red}silhouette-$ 的优秀写法。

----

**一些例题：**

[[NOI2018] 归程](https://blog.csdn.net/sharp_legendgod/article/details/120678041)

[[IOI2018] werewolf 狼人](https://blog.csdn.net/sharp_legendgod/article/details/120677933)


