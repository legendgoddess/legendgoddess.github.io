---
title: 整除分块
date: 2022-3-30 7:31:00
tags:
  - 数论
categories:
  - 算法
---

整除分块就是一种可以将相同值一起计算，使得带有取上整或者取下整的式子可以在 $O(\sqrt n)$ 内计算。

---

我们考虑一种比较简单的式子：

$$
\sum_{i = 1}^ n \lfloor \frac{n}{i}\rfloor
$$

> 直接进行打表可以发现有一些部分是相同的，我们考虑一起计算。

不妨考虑 $\left \lfloor\dfrac{n}{i} \right\rfloor = k, n = ki + r$。

考虑会带得到 $\dfrac{n - r}{k} = i$，考虑 $r \in (0, i)$，而且我们有 $\left \lfloor\dfrac{n}{i} \right\rfloor = k$。

所以我考虑如果想要让 $i$ 最大，我们必须满足 $r = 0$，所以我们有 $max(i) = \left \lfloor \dfrac{n}{\dfrac{n}{i}} \right\rfloor$。

我们仔细思考复杂度，每次让 $\lfloor\dfrac{n}{i}\rfloor$ 不同的第一个 $i$ 必定是 $n$ 的因子，因为 $n$ 总共只有 $O(\sqrt n)$ 个因子，所以复杂度是 $O(\sqrt n)$ 的。

---

$\tt \color{red} \large  \text{Stronger conclusion}$

如果这个式子不是取下整怎么办？

我们还是只考虑 $\left \lceil \dfrac{n}{i} \right \rceil$，我们先将其转化成下取整有 $\left \lceil \dfrac{n}{i} \right \rceil = \left \lfloor \dfrac{n + i - 1}{i} \right \rfloor = \left \lfloor \dfrac{n - 1}{i} \right \rfloor - 1$。

考虑 $i$ 取到最值就是 $\left \lfloor \dfrac{n - 1}{\dfrac{n - 1}{i}} \right \rfloor$，考虑将取下整变成取上整，可以得到：

$$
\left \lceil \frac{n}{\lceil \frac{n}{i} \rceil - 1} \right \rceil
$$

这个就是我们的最终式子了。
