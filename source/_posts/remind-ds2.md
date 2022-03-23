---
title: 数据结构回忆2
date: 2022-3-21 21:05:20
tags:
  - 线段树
  - 平衡树
categories:
  - 专题
---

[Legendgod's Blog](https://legendgod.ml/)

---

## 树状数组

一个函数 $\tt lowbit(x) = x \& (-x)$ 表示 $x$ 的最低二进制位的位置。

可以维护区间加区间和，常数比较**小**。

- $O(n)$ 建树，考虑对于节点 $x$ 向上更新 $x + \tt lowbit(x)$ 即可。

- 查询第 $K$ 小，就是经典二进制上二分，假设元素 $i$ 出现了 $a_i$ 次。

```cpp
int Kth(int k) {
    int cnt = 0, ret = 0;
    for(int i = log2(n); ~ i; -- i) {
        ret += (1 << i);
        if(ret >= n || cnt + t[ret] >= K) ret -= (1 << i);
        else cnt += ret;
    }
    return ret + 1;
}
```

- 时间戳优化，听起来听高级的，其实就是打标记看当前时刻有没有经过，经过的时候动态赋初值即可。

## 线段树

常常是可以维护满足如下信息的：

- 结合律，有 $a \otimes b \otimes c = a \otimes (b \otimes c)$。

- 封闭性，$\forall x, y \in S, x \otimes y \in S$。

- 存在单位元。

$\tt \text{广义线段树}：$

常规的线段树的 $\tt mid = \frac{l + r}{2}$，但是广义的线段树 $\tt mid$ 可以是任意的合法值。

### 线段树结构题

> 用于解决模拟线段树上的操作上的问题。

1. $[1, n]$ 的广义线段树 $[l, r]$ 内的区间定位数是 $2 \times (r - l+ 1) - |S|$，其中 $S$ 表示当前区间的子区间个数，区间定位数表示 $[l, r]$ 最少拆成多少线段树上区间的并。

取出所有在 $[l, r]$ 内的节点，显然构成了一个满二叉树森林，其中根的个数就是 $[l, r]$ 的区间定位数。

对于满二叉树其节点个数为 $2m -1$ 其中 $m$ 为叶子节点的个数。

所以总点数就是 $2(r - l + 1)  - ans = |S|$，其中 $ans$ 表示联通块的个数，那么 $\tt ans$ 就是根的数量，也就是区间定位数，可以得到 $ans = 2(r - l + 1) - |S|$。

2. $[1, n]$ 的线段树 $[l, r]$ 内的区间定位数是由 $[l - 1, l - 1], [r + 1, r + 1]$ 的 $\tt Lca$ (设其分别为 $L, R$) 到 $L$ 路径上的所有节点的右边一个的兄弟，和到 $R$ 路径上的所有节点的左边一个兄弟。

考虑设 $P$ 到所有祖先里面的左偏儿子的右兄弟集合为 $LFT(P)$ 右偏儿子的左兄弟的集合为 $RGT(P)$。

对于 $L, R$ 的 $\tt Lca$ 节点 $S$，其区间定位可以表示成如下的形式：

$$
(LFT(L) \setminus LFT(ls_S)) \cup (RGT(R) \setminus RGT(rs_S))
$$

3. 跳跃树，对于**广义线段树区间定位**更加强的结构。

> 这个东西之后再详细研究。[NOI 一轮复习 III：数据结构 - ix35_ 的博客 - 洛谷博客](https://www.luogu.com.cn/blog/ix-35/noi-yi-lun-fu-xi-iii-shuo-ju-jie-gou)

## 线段树技巧

- 动态开点，可以考虑开内存池。

- 线段树合并，复杂度是两棵树中节点较少的 $O(\text{节点数})$。

- 线段树二分，信息具有二分性即可。

- 线段树分裂，与线段树合并类似，可以使得权值线段树按照第 $\tt K$ 大进行分裂。

- 线段树合并优化卷积，考虑 $\sum_i \sum_j val(i, j)$ 这样的式子，下标其实无所谓，但是重点是下标的上下界是明确的，比如说 $\sum_{i = 1} ^ n \sum_{j = i + 1} ^ n val(i ) \times val(j)$，这个东西可以看成对于一个 $j$ 使用左边的所有 $i$ 去更新它。

> 因为我比较菜，所以后面内容可能就是按照别人的博客写了，[线段树相关技巧的小小总结 - 木xx木大 的博客 - 洛谷博客](https://www.luogu.com.cn/blog/flyingfan/xian-duan-shu-xiang-guan-ji-qiao-di-xiao-xiao-zong-jie)

- 线段树维护树直径：

可以考虑贪心，合并两个点集的时候，新的直径肯定是被合并的两个端点其中的一个。

> **不能有负权边**，这个常常会在一些题目，特别是**图**的离线算法并查集合并的时候出现。

考虑将点拍扁到**欧拉序**上，两点的距离就是 $dep_u + dep_v - 2\min_{i = u} ^ vdep_i$，这个就是维护区间 $\tt min, max$，还有 $lmin = dep_u - 2 \min_{i = l} ^ udep_i$，$rmin$ 同理。

合并的时候要么全部都在左边，要么全部在右边，要么跨越区间的端点。

```cpp
void pushup(int p) {
    t[p].mx = max(t[ls].mx, t[rs].mx), t[p].mn = min(t[ls].mn, t[rs].mn);
    t[p].lm = max((t[ls].lm, t[rs].lm), t[rs].mx - 2 * t[ls].mn);
    t[p].rm = max((t[ls].rm, t[rs].rm), t[ls].mx - 2 * t[rs].mn);
    t[p].ans = max(max(t[ls].ans, t[rs].ans), max(t[ls].mx + t[rs].lm, t[rs].mx + t[ls].rm));
}
```

- 线段树维护单调栈

[楼房重建 - 洛谷](https://www.luogu.com.cn/problem/P4198)

本质就是维护前缀最大值的个数。

设 $s$ 表示前缀最大值的个数，$mx$ 表示区间最大值，设 $Solve(p, x)$ 表示 $p$ 覆盖的区间中，考虑前缀最大值 $x$ 后的区间严格最大值个数。

如果 $x < mx_{ls}$，处于右区间的前缀最大值必定大于 $mx_{ls}$，所以右区间不能造成影响，答案为 $ solve(ls, x) + s_{p} - s_{ls}$。

如果 $x \ge mx_{ls}$，做区间的所有数都不可能成为前缀最大值，答案为 $solve(rs, x)$。

每次只往一遍递归复杂度是 $O(\log n)$ 的。

如果维护的信息是不具有可减行性 ( 如取 $\tt max, min$ 等 )，考虑更加强的定义：

$s$ 表示只考虑 $[l, r]$ 区间的影响时，$[mid + 1, r]$ 中的答案，那么 $solve$ 函数中的第一种情况可以改为 $solve(ls, x) + s_{p}$，更新答案的操作为 $s_{p} = solve(rs, mx_{ls})$，查询操作为 $solve(rt, 0)$。


