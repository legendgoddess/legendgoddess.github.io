---
title: "[TJOI2015] 概率论"
date: 2022-02-22 18:52:21
tags:

- 数论
- 生成函数
  categories:
- 省选题解

---

<h3><center>[TJOI2015] 概率论</center></h3>

> [题目地址](https://loj.ac/p/2105)

首先考虑将期望变成每种方案的叶子总数除以所有的方案。

那么我们不妨先考虑所有的方案需要怎么算，也就是 $n$ 个节点二叉树的数量。

不妨设 $f(n)$ 表示上述的量，可以得到方程 $f(n) = \sum_{i = 0} ^ {n - 1} f(i) \times f(n - 1 - i)$，其中 $f(0) = 1$。

那么我们可以得到生成函数 $F(x) = F^2(x) \times x+ 1$。

之后解得 $F(x) = \frac{1 \pm \sqrt{1 - 4x}}{2x}$。

我们将上面的东西向下除就可以得到 $\frac{2}{1 \pm \sqrt{1 - 4x}}$ 将 $f(0) = 1$ 带入可以得到下面取到的是 $+$，那么上面的就是 $-$。

所以我们可以得到 $F(x) = \frac{1 - \sqrt{1 - 4x}}{2x}$。

我们将其展开可以得到 $\frac{1}{2x} \times (1 - \sqrt{1 - 4x})$。

$$
\begin{aligned}
F(x) &= \frac{1}{2x} \times (1 - \sqrt{1 - 4x}) \\
&= \frac{1}{2x} \times (1 - \sum_{i \ge 0} \binom{\frac{1}{2}}{i} (-4x)^i)\\
&= \frac{1}{2x} \times (- \sum_{i \ge 1} \binom{\frac{1}{2}}{i} (-4x)^i) \\
&= 2\times \sum_{i \ge 1} \binom{\frac{1}{2}}{i} (-4x)^{i - 1} \\
&= \sum_{i \ge 1} \frac{(2i - 2)!}{(i - 1)! \times i!} (x)^{i - 1}\\
&= \sum_{i \ge 0} \binom{2i}{i} \times \frac{1}{i + 1}x^i\\
&= \sum_{i \ge 0} \frac{\binom{2i}{i}}{i + 1}x^i\\
\end{aligned}
$$

事实上这个东西就是卡特兰数。

我们之后考虑上面的部分，不妨考虑一边子树的叶子个数为 $x$ 那么其会被计算右边子树的个数次，那么我们的方程就是很好拿出来了。

不妨设 $g(n)$ 表示 $n$ 个点所有树的叶子个数之和。

可以得到：

 $g(n) = \sum_{i = 0} ^ {n - 1} g(i) \times f(n - i - 1) + g(n - i - 1) \times f(i) $

$g(n)=  2 \sum_{i = 0} ^ {n - 1} g(i) \times f(n - i - 1)$ 

首先 $g(0) = 0, g(1) = 1$。但是 $g(1)$ 不能用 $g(0)$ 进行推导，那么我们就需要额外增加一项 $[n == 1]$。

设答案的生成函数为 $G(x)$ 可以得到：

$$
\begin{aligned}
G(x) &= 2G(x)F(x) x + \sum_{i \ge 0} [n == 1] x^i\\
&= 2G(x)F(x) + x
\end{aligned}
$$

之后我们移项得到 $G(x) = \frac{x}{1 - 2xF(x)}$带入可以得到。

$$
\begin{aligned}
G(x) &= \frac{x}{\sqrt{1 - 4x}} \\
&= x \times (1 - 4x) ^ {-\frac{1}{2}} \\
&= x \times \sum_{i \ge 0} \binom{-\frac{1}{2}}{i} (-4x) ^ i \\
&= x \times \sum_{i \ge 0} \binom{2i}{i} x^i \\
&= \sum_{i \ge 0} \binom{2i}{i} x^{i + 1}\\
\end{aligned}
$$

所以 $[x^n] G(x) = \binom{2(i - 1)}{i - 1}$。

那么答案就是：

$$
\begin{aligned}
\frac{\binom{2(n - 1)}{n - 1}}{\frac{\binom{2n}{n}}{n + 1}} &= \frac{n \times (n + 1)}{4n - 2}
\end{aligned}
$$

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
//		#define Fread
		#ifdef Fread
		const int Siz = (1 << 21) + 5;
		char *iS, *iT, buf[Siz];
		#define gc() ( iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iS == iT ? EOF : *iS ++) : *iS ++ )
		#define getchar gc
		#endif
		template <typename T>
		void r1(T &x) {
		    x = 0;
			char c(getchar());
			int f(1);
			for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
			for(; isdigit(c); c = getchar()) x = (x * 10) + (c ^ 48);
			x *= f;
		}
		template <typename T, typename...Args>
		void r1(T &x, Args&...arg) {
			r1(x), r1(arg...);
		}
		#undef getchar
	}

using namespace Read;

const int maxn = 2e5 + 5;

double n;

signed main() {
	int i, j;
    r1(n);
    double ans = n * (n + 1) / (4 * n - 2);
    printf("%.9lf\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }

```

> 这道题是第一次自己独立推出来的题目，还是很有纪念意义的。