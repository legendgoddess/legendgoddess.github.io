---
title: 分析时间复杂度
date: 2021-09-01 09:43:23
tags:
  - 时间复杂度
categories:
  - 算法
---
> emmm，先说明一下，作者其实不是很会，有些问题请指出。

---

$\color{red}\tt \text{定义}$

$\Theta(f(n))$ 表示时间复杂度渐进的上下界。

$\Omega(f(n))$ 表示时间复杂度的下界。

$O(f(n))$ 表示时间复杂度的上界。

---

$\color{red}\tt Master\ Theorem$

设递推式 $T(n) = aT\left(\dfrac{n}{b}\right) + f(n)$。

设常数 $\epsilon > 0, k \ge 0$，

$$
T(n) = 
\begin{cases}
\Theta(n^{\log_ba}), f(n) = O(n^{\log_ba - \epsilon}) \\
\Theta(f(n)), f(n) = O(n^{\log_ba + \epsilon}) \\
\Theta(n^{\log_ba}\log^{k + 1}n), f(n) = O(n^{\log_ba} \log^kn)
\end{cases}
$$

也就是考虑递归的时候相邻两层之间的公比，我们是取分子分母中比较大的那个，特殊的情况就是公比为 $1$ 的时候，也就是需要再乘上一个递归的层数。

$\color{red}\tt Akra-Bazzi\ Theorem$

设 $T(n) = \sum_{i = 1}^n a_iT\left(\dfrac{n}{b_i}\right) + f(n)$。

$$\exists p, \sum_{i = 1}^n \dfrac{a_i}{b_i ^p} = 1$$

$$T(n) = \Theta\left(n^p\left(\int_1^n\dfrac{f(x)}{x^{p + 1}}dx\right)\right)$$

$\color{red}\tt \text{递归树方法}$

具体来说就是首先分析每一层的节点大小，之后分析每一层的节点个数，然后求出总共有几层即可。

**例题**

$$T(n) = \sqrt nT(\sqrt n) + O(n)$$

首先分析根号，就是开 $\log_2 \lg n$ 次 $n$ 就变成 $1$ 了。所以容易得到层数是 $\log_2 \lg n$。

然后第 $i$ 层的节点大小就是 $n^{\frac{1}{2^i}}$ 每一层的节点数量就是 $n^{2^i}$ 然后每一层的贡献就是 $O(n)$，那么总的时间复杂度就是 $O(n \log_2 \lg n)$。

如果出现节点数量和层数不明显的情况我们可以使用递推式解决这个问题。

$\color{red} \tt \text{势能分析法}$

使用这种办法多半是因为每次操作的复杂度是不固定的，需要求一个均摊复杂度。

我们不妨设第 $i$ 次实际操作的复杂度是 $c_i$ 均摊复杂度是 $a_i$ 势能为 $\phi_i$ 那么根据定义就是 $a_i = c_i + \Delta\phi_i$。之后实际上就是对于每种情况进行暴力分讨。

对于势能究竟需要怎么设才行，我们不妨考虑对于整体的时间复杂度被什么因素影响。

比如说经典的栈进出问题，本质上就是当前栈内有多少个元素。

对于 $Splay$ 的旋转分析问题，考虑 $bst$ 的本质，如果对于每一个节点都查找一个次是每个节点的深度，但是进行旋转的时候节点深度变化不会很方便处理，那么显然这个节点深度和也就是等于每个节点的子树大小之和。至于使用什么函数进行表达这个就是看运算的方便了。

参考文章：[$\tt Parsnip \text{学长的博客}$](https://www.cnblogs.com/Parsnip/p/13768326.html)。
