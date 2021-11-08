---
title: CF449C Jzzhu and Apples 题解
date: 2021-10-01 08:19:24
tags:
  - 构造
categories:
  - CF题解
---

<h3><center>CF449C Jzzhu and Apples</center></h3>

> [CF449C Jzzhu and Apples](https://www.luogu.com.cn/problem/CF449C)

肯定会想到偶数肯定需要来匹配的，之后发现对于奇数的情况，对于每一个奇数肯定都是若干个质数的倍数。那么越大的质数越难被匹配，我们考虑从大到小对于质数进行考虑。

对于当前质数 $p$ 找到所有没有被匹配过的数，如果有偶数个直接进行匹配即可，不然的话肯定需要舍弃一个数，发现 $2$ 是一个质数，那么我们舍弃一个 $2$ 的倍数即可。

这样可以发现匹配到最后我们最多舍弃一个数，不可能更优了。




