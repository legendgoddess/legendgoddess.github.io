---
title: "3.19 下午作业"
date: 2022-3-19 10:41:00
password: fuyimin
tags:
  - 数据结构
categories:
  - 专题训练
  - 数据结构
---

3.19日下午14：00 。
讲以下题，请大家上午安排时间思考预习。

- [ ] CF679E Bear and Bad Powers of 42 [Bear and Bad Powers of 42 - 洛谷](https://www.luogu.com.cn/problem/CF679E)

**简要题意**：定义一个正整数是坏的，当且仅当它是 $42$ 的次幂，否则它是好的。

给定一个长度为 $n$ 的序列 $a_i$​，保证初始时所有数都是好的。

有 $q$ 次操作，每次操作有三种可能：

- `1 i` 查询 $a_i$​。
- `2 l r x` 将 $a_{l\dots r}$​ 赋值为一个好的数 $x$。
- `3 l r x` 将 $a_{l\dots r}$​ 都加上 $x$，重复这一过程直到所有数都变好。

$n,q≤10^5，a_i​,x≤10^9。$

**Solution**: 如果不考虑区间覆盖可以发现直接暴力维护即可，但是因为我们区间复制会多增加一个数，我们将相同的数统一处理即可。对于每一个数维护最近的 $42$ 的幂次减去当前的值。考虑通过像珂朵莉树一样的暴力处理区间，之后对于这样的区间 $[l, r]$ 考虑只需要考虑 $r$ 节点的权值，在线段树上将 $[l, r - 1]$ 全部赋值成 $\tt +\infty$，之后暴力做就可以了。

- [ ] CF571D Campus [Campus - 洛谷](https://www.luogu.com.cn/problem/CF571D)

**题意描述：**

有一个长度为 $n$ 的序列，初始全为 $0$。  
有两类对下标的集合，初始时每一类各有 $n$ 个集合，编号为 $i$ 的集合里有下标 $i$。  
一共有 $m$ 个操作，操作有五种：  
1. `U x y` 将第一类编号为 $y$ 的集合合并到编号为 $x$ 的集合里。  
2. `M x y` 将第二类编号为 $y$ 的集合合并到编号为 $x$ 的集合里。  
3. `A x` 将第一类编号为 $x$ 的集合中的所有下标在序列中对应的数加上 $x$ 的集合大小。  
4. `Z x` 将第二类编号为 $x$ 的集合中的所有下标在序列中对应的数设为 $0$。  
5. `Q x` 询问序列中下标为 $x$ 的位置上的数。  
$n,m \le 5 \times 10^5$。

**Solution:** 建立重构树，考虑是单点查询，两棵树是独立的，当前答案就是总共的答案减去前面一段时间的答案，建立主席树即可。

- [ ] CF407E k-d-sequence [k-d-sequence - 洛谷](https://www.luogu.com.cn/problem/CF407E)

- [x] CF453E Little Pony and Lord Tirek [Little Pony and Lord Tirek - 洛谷](https://www.luogu.com.cn/problem/CF453E)

- [ ] CF1332G No Monotone Triples [No Monotone Triples - 洛谷](https://www.luogu.com.cn/problem/CF1332G)

**Solution:** 首先子序列的长度是 $3, 4$，考虑反证法。

- [ ] CF960H Santa's Gift [Santa&#039;s Gift - 洛谷](https://www.luogu.com.cn/problem/CF960H)

- [ ] CF1344E Train Tracks [Train Tracks - 洛谷](https://www.luogu.com.cn/problem/CF1344E)

- [ ] CF487E Tourists [Tourists - 洛谷](https://www.luogu.com.cn/problem/CF487E)

**简要题意：** 给定一张 $n$ 个点 $m$ 条边的无向图，第 $i$ 个城市的点权为 $w_i$，有修改单点点权，求 $a \to b$ 所有路径上的最小点权。

**Solution**: 考虑建立一个圆方树，直接进行树剖线段树维护，对于一个方点其权值就是所有周围节点的权值，考虑修改一个圆点的时候直接对于**父亲**的方点进行更新即可。 对于 $\tt Lca$ 是方点的时候还要考虑父亲的圆点。

- [ ] CF643G Choosing Ads [Choosing Ads - 洛谷](https://www.luogu.com.cn/problem/CF643G)

- [ ] CF1017G The Tree [The Tree - 洛谷](https://www.luogu.com.cn/problem/CF1017G)

- [ ] CF639F Bear and Chemistry [Bear and Chemistry - 洛谷](https://www.luogu.com.cn/problem/CF639F)

- [ ] UOJ 637. [美团杯2021] A. 数据结构 [【美团杯2021】A. 数据结构 - 题目 - Universal Online Judge](https://uoj.ac/problem/637)

- [ ] CF1270H Number of Components [Number of Components - 洛谷](https://www.luogu.com.cn/problem/CF1270H)

- [ ] CF704E Iron Man [Iron Man - 洛谷](https://www.luogu.com.cn/problem/CF704E)

<embed src="/image/2022-3-19.pdf" type="application/pdf" style="overflow: auto; width: 100%; height: 600px"/>'
