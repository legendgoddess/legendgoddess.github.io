---
title: 生成函数浅谈
date: 2022-02-28 14:34:00
tags:
  - 数论
  - 生成函数
  - 卷积
categories:
  - 算法
---

> [多项式计数杂谈 - command_block 的博客](https://www.luogu.com.cn/blog/command-block/sheng-cheng-han-shuo-za-tan) 学习总结

## 二项式

- $\color{red}(1)$ **拆开组合数**

$$
\binom{n}{m} = \frac{n!}{m!(n - m)!}
$$

组合意义就是总共有 $n$ 个物品，我们选择 $m$ 个出来的方案数。

根据组合意义，我们需要有 $0 \le m \le n$ 才能保证成立。

一些**推论**：

1. 对称性 $\binom{n}{m} = \binom{n}{n - m}$

2. 吸收恒等式 $\binom{n}{m} = \frac{n}{m}\binom{n - 1}{m - 1}$
- $\color{red}(2)$ **经典二项式定理**

$$
(x + y)^k = \sum_{i = 0} ^ k \binom{k}{i}x^iy^{k - i}
$$

**注意** $1$ 的幂可能隐藏在求和中。

- $\color{red}(3)$ **递推公式**

$$
\binom{n}{m}= \binom{n - 1}{m} + \binom{n - 1}{m - 1 }
$$

> 证明的话就是考虑新加入一个数到底是放不放的情况。

对于更加复杂的二项式的形式，我们可以考虑通过拆开进行递推得到答案。

一些**推论**：

- **平行求和**

> $$
> \sum_{i = 0} ^ n \binom{r + i}{i} = \binom{r + n + 1}{n}
> $$

- **上指标求和**

> $$
> \sum_{i = 0} ^ n \binom{i}{m} = \binom{n + 1}{m + 1}
> $$

- **下指标求和**

> $$
> \sum_{i = 0} ^ n \binom{n}{i} = 2^n
> $$

- **对称性**

> 奇数项和偶数项分别等于 $2^{n - 1}$。
> 
> $2^{n - 1} = \sum_{i = 0} ^ {\lfloor\frac{n}{2}\rfloor} \binom{n}{i} + \frac{1}{2} \times \binom{n}{\frac{n}{2}}[n\ is\ even]$

- $\color{red}(4)$ **范德蒙德卷积**

$$
\sum_{i = 0} ^ k \binom{n}{i} \binom{m}{k - i} = \binom{n + m}{k}
$$

本质上就是 $(1 + x)^n \times (1 + x)^m = (1 + x)^{n + m}$。

或者从组合意义上理解也是一样的，就是在两个集合选 $k$ 个数的方案。

- $\color{red}(5)$ **广义二项式系数 + 广义二项式定理**

定义**广义二项式系数**：$\binom{n}{m} = \frac{n^{\underline {i}}}{m}$。

这样我们就有：

$$
(x + y)^a = \sum_{i \ge 0} \binom{a}{i} x^i y^{a - i}
$$

广义二项式定理**衍生技巧**：

- **上指标反转**：

$$
\binom{n}{m} = \binom{m - n - 1}{m} (-1)^m
$$

- **取一半**：处理 $\binom{2n}{n}$ 形组合数。

我们考虑两个下降幂的乘积：

$$
x^{\underline {k}}(x - \frac{1}{2})^{\underline{k}} = x(x - \frac{1}{2})(x - 1) \cdots (x - k + 1)(x - k + \frac{1}{2})
$$

考虑加倍操作得到：

$$
x^{\underline {k}}(x - \frac{1}{2})^{\underline{k}} \times \frac{1}{2^{2k}} = \frac{(2x)^{\underline{2k}}}{2^{2k}}
$$

我们两边同时除以 $(k!)^2$ 得到：

$$
\binom{x}{k} \binom{x - \frac{1}{2}}{k} = \binom{2x}{2k} \binom{2k}{k} \times \frac{1}{2^{2k}}
$$

> 说句实话这里 $\tt command-block$ 写错了。

考虑带入 $x = n, k = x$ 可以得到等式：

$$
\binom{n - \frac{1}{2}}{n} = \binom{2n}{n} \times \frac{1}{2^{2n}}
$$

也就是：

$$
\binom{- \frac{1}{2}}{n} \times 2^{2n} \times (-1)^n = \binom{2n}{n}
$$

最终得到：

$$
\binom{-\frac{1}{2}}{n} \times (-4)^n = \binom{2n}{n}
$$

可以求出 $\sum_{i = 0} \binom{2i}{i} z^i$ 的封闭形式：

$$
\sum_{i = 0} ^ {\infty} \binom{-\frac{1}{2}}{i} \times (-4z)^i = (1 - 4z) ^ {-\frac{1}{2}}
$$

- $\color{red}(6)$ **下降幂和上升幂的二项式定理**

$$
(x + y) ^{\underline{a}} = \sum_{i = 0} ^ n \binom{n}{i} x^{\underline i}y^{\underline{a - i}}\\\\
(x + y) ^{\overline{a}} = \sum_{i = 0} ^ n \binom{n}{i} x^{\overline i}y^{\overline{a - i}}
$$

- $\color{red}(7)$ **二项式反演**

见 [二项式反演](https://legendgod.ml/2021/09/01/binomial-inversion/)。

- $\color{red}(8)$ **差分和前缀和**

**前缀和**：乘以一个全是 $1$ 的多项式，也就是 $\frac{1}{1 - z}$。

**差分**：乘以 $1 - z$。

- $\color{red}(9)$ **牛顿级数**

> 暂时不是很会，等会了再说，感觉不是很难。

- $\color{red}(10)$ **生成函数和杨辉三角**

考虑二元生成函数的形式：

$$
\begin{aligned}
G[n, m] &= \binom{n}{m} \\\\
G(x, y) &= \sum_{i = 0} \sum_{j = 0} \binom{i}{j} x^i y^j \\\\
G(x, y) &= \sum_{i = 0} x^i (y + 1) ^ i \\\\
G(x, y) &= \sum_{i = 0} (xy + x)^i \\\\
G(x, y) &= \frac{1}{1 - x - xy}
\end{aligned}
$$

令 $y = x^k$ 则 

$$
\begin{aligned}
[x^n]G(x, y) &= [x^n]\sum_{i = 0}\sum_{j = 0} \binom{i}{j} x^i y^j\\\\
&= [x^n]\sum_{i = 0}\sum_{j = 0} \binom{i}{j} x^i x^{jk}\\\\
&= \sum_{i + jk = n} \binom{i}{j}
\end{aligned}
$$

同理如果 $x = y^k$ 同样可以得到 $[x^n]G(x, y) = \sum_{ki + j = n} \binom{i}{j}$。

---

- $\color{red}(1)$ **特征方程**

如果有递推方程 $f(n) = af(n - 1) + bf(n - 2)$。

设 $x^2 = ax + b$ 解得两个根 $x_0, x_1$。

我们断言 $f(n) = Ax_0^n + Bx_1^n$。

我们带入初始给定的值即可解得方程。

- $\color{red}(2)$ **构造系数的技巧**
1. **平移** $[x^n]F(x)x^t = F[n - t]$。

2. **伸缩** $F(x^k) \leftarrow <F[0], \overbrace{0, 0, \cdots, 0, 0}^{k - 1 \text{个 0}}, F[1],\overbrace{0, 0, \cdots, 0, 0}^{k - 1 \text{个 0}} \>$。
- $\color{red}(3)$ **生成函数系数的获取**

考虑通过求导和平移用 $F(x)$ 表示 $F'(x)x$，然后得到与 $F(x), F'(x)$ 的递推式。

考虑将其表示成系数的形式显然有 $F'[n] = (n + 1)F[n + 1]$。

带入即可得到递推式。

可以考虑例题 [[MtOI2018]情侣？给我烧了！（加强版）](https://www.luogu.com.cn/problem/P4931)

其中求 $D(x)$ 的时候可以使用该方法。

> 所以我才能切了这题。

当然如果你得到通向了之后可以暴力展开。

---

或者考虑对于比较复杂的部分 $\frac{C + \sqrt{x}}{x}$ 这样的情况，我们可以考虑分步进行计算。

考虑设 $G(x) = \sqrt{x}$ 我们先求出 $G$ 的递推式，之后可以得到 $F$ 关于 $G$ 的式子，之后就可以直接得出答案了。

**例题1**：

$$
F(x) = \frac{1 - x - \sqrt{1 - 6x + x^2}}{2x}
$$

求其递推式。

设 $G(x) = \sqrt{1 - 6x + x^2}$

可以知道 $G'(x) = \frac{1}{2}(1 - 6x + x^2) ^ {-\frac{1}{2}}\times (-6 + 2x)$

我们两边同时乘以 $1 - 6x + x^2$。

可以得到 $G'(x)(1 - 6x + x^2) = G(x)\times(x - 3)$。

也就是 $g'[n]-6g'[n-1]+g'[n-2]=g[n-1]-3g[n]$。

根据我们之前的式子 $g'[n]=g[n + 1] (n + 1)$。

可以知道 $g[n + 1](n + 1) - 6ng[n]+(n-1)g[n-1]=g[n-1]-3g[n]$。

整理之后得到：

$$
\begin{aligned}
g[n+1]&(n+1)-(6n-3)g[n]+(n-2)g[n-1]=0\\\\
g[n+1]&=\frac{1}{n+1} (6n-3)g[n]-(n-2)g[n-1]
\end{aligned}
$$

> 初值什么的就自己看了。

之后考虑 $F(x) = \frac{1-x-G(x)}{2x}$。

可以得到 $2xF(x)=1-x-G(x)$。

发现前面的 $1-x$ 本质上只和 $f(1)$ 有关。

所以得到 $2f(n)=g(n + 1)$。

- $\color{red}(4)$ **生成函数的组合意义**

$\tt exp$ 表示有标号无交集合的拼接，注意我们可以通过钦定根的情况直接使用 $\tt exp$ 来得到一些有根的图或者树。

$\tt ln$ 这个就是 $\tt exp$ 的逆操作，也就是将一个集合分成若干个不相交的集合，常见的应用就是将无向图变成连通的。

> 这个操作应该是由连通图推到无向图，之后再取对数得到的。
> 
> 而不是本来 $\tt ln$ 的意义！！
