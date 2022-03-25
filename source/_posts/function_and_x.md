---
title: 多项式浅谈
date: 2021-04-10 18:14:42
tags:
  - 多项式
categories:
  - 算法
---


$$
\Large\color{blue} Jhb\color{red}\text{的多项式浅谈}
$$

> 这个的后半部分似乎可以当做金策大佬论文的人话解读。

推销一下[窝的博客](https://www.luogu.com.cn/blog/legendgod/zhang-ji-xiang-mu-duo-xiang-shi-ji-lie),本文所有的代码都在窝的博客上。

---

多项式主要的用途是用来优化式子，因为笔者也刚刚入门多项式，可能谈到的内容也十分浅，有错请直接开 D。



我们从大整数乘法入手，我们经常写的算法是通过枚举两个整数的每一位，通过位权来更新答案，这里显然是 $O(n^2)$ 。还有一种是分治乘法，还是通过位权，原理是 ：
$$
(Ax^m + B)(Cx^m + D) =ACx^{2m} + ((A + B)(C +D) - AC - BD))x^m + BD
$$
我们直接分治可以算出答案复杂度 $O(n^{2})$ 复杂度比较优秀？其实这种想法和之后的 MTT 优化是相同的。其中 n 表示位数。

> 复杂度怎么算？ $T(n) = 4T(\frac{n}{2}) + O(n)$
>
> $T(n)  = O(n^2)$

之后我们考虑优化 $ACx^m + ((A - B)(D - C) + AC + BD) \times x^{\frac{n}{2}} + BD$ 这边只有 3 个乘积可以考虑优化 $T(n) = 3T(\frac{n}{2}) + O(n) = O(n^{\log_23})$ 可以接受。

> 主定理：
> $$
> T(n) = aT(\lceil\frac{n}{b}\rceil) = O(n^d)\\
> T(n) = 
> \begin{cases}
> O(n^d), d > log_ba \\
> O(n^d \log n), d = log_b a\\
> O(n^{log_ba}), d < log_ba
> \end{cases}
> $$
>

但是我们可以我们 FFT 将系数表示法变成点值表示法，从而得到答案复杂度 $O(n \log n)$。

我们常用的表示法是系数表示法 $f(x) = \sum_{i = 0} a_i x^i $  我们可以从中取 n + 1 个点来表示这个多项式，也就是 $f(x) = \{a_0,a_1\dots a_n\}$

DFT 就是把一个多项式从系数转化成点值，IDFT 反之。

## FFT

对于两个个点值表示法的多项式 $f(x) = (x_0, f(x_0)) \dots (x_n, f(x_n))$ 和 $g(x) = (x_0, g(x_0)) \dots (x_n,g(x_n))$

设 $F(x) = f(x) \cdot g(x) = (x_0, f(x_0)g(x_0)) \dots (x_n, f(x_n)g(x_n))$  可以看出我们能够快速得将其相乘。

但是直接暴力 DFT,IDFT 是 $O(n^2)$ 的，有些暴力还是用高斯消元。。。

我们发现 $x^i$ 在 $x = 1,-1$ 的时候是很简单的，这会让我们想到圆，之后我们需要 n + 1 个数，那么我们不妨用模长为 1 的复数。

$\omega_n = e^{\frac{2\pi i}{n}}$ 则 $x^n = 1$ 的解集为 $\{\omega_n^k|k = 0,1\dots n - 1\}$ 也就是将一个圆分成 n 等分。

$\omega_n = e^{\frac{2\pi i}{n}} = cos (\frac{2\pi}{n}) + i \cdot sin(\frac{2\pi}{n})$

$\text{单位圆}$

在一个复平面上，以原点为圆心, 1 为半径作圆，所得到的圆就是单位圆，将其 n 等分（ n 等分点就是终点），形成 n 个向量，设幅角为正且最小的向量 $w_n$ ，称为 n 次单位根

剩下的复数为 $w_n^2 \dots w_n^n$

$w_n^0 = w^n_n = 1$

$w_n^k = \cos k \times \frac{2\pi}{n} + i \sin k \times \frac{2\pi}{n}$

单位根的幅角为周角的 $\frac{1}{n}$

$w_n^{\frac{n}{2} + k} = - w_n^k$

> 证明考虑将其展开 $w_n^{\frac{n}{2}} \times w_n^k$
>
> 将左边的那项展开得到 $-1$

$(w^k_n)^2 = w_{\frac{n}{2}}^k$

> 证明考虑，复数相乘，幅角相加，模长为 $1 \times 1 =1$ 因为都是单位根

---

$FFT​$

首先我们先假设一个多项式的系数 ~~假设 n 为偶数~~ $A(x) = (a_0,a_1,a_{n - 1})$

$$
A(x)=a_0+a_1*x+a_2*{x^2}+a_3*{x^3}+a_4*{x^4}+a_5*{x^5}+ \dots+a_{n-2}*x^{n-2}+a_{n-1}*x^{n-1}
$$

我们把它的下标按奇偶性分类
$$
\begin{cases}
A(x) = \sum_{i = 0} ^ {n -1} a_i x^i \\\\
A_1(x) = \sum_{i = 0} ^ {m - 1} a_{2i} x^{2i} \\\\
A_2(x) = \sum_{i = 0} ^ {m - 1}a_{2i + 1} x ^{2i + 1} \\\\
m = \frac{n}{2}
\end{cases}
$$
所以我们可以得到

$A(x) = \sum_{i = 0} ^ {m - 1}a_{2i} (x^2) ^i + x\sum_{i = 0} ^ {m - 1} a_{2i + 1} (x^2) ^ i$

所以我们可以将其分成两部分进行递归，也就是 $A(x) = A_1(x^2) + x A_2(x^2)$

但是根据主定理，这是 $O(n^2)$

我们考虑 $A(w^k_n) = A_1((w_n^k)^2) + w_n^k A_1((w_n^k)^2)$

> $(w_n^k)^2 = w_{\frac{n}{2}} ^ k$

所以可以化简
$$
\begin{aligned}
m &= \frac{n}{2} \\\\
A(w_n^k) &= A_1(w_m^k) + w_n^kA_2(w^k_m) \\\\
&\text{之后来看后半部分} \\\\
A(w_n^{k + m}) &= A_1(w_m^k) - w_{n}^kA_2(w_m^k) 
\end{aligned}
$$
发现只要知道了 RHS 就可以直接求出， 复杂度为 $O(n \log n)$

这叫蝴蝶变换

---

$IDFF$

这里就只想结论 $B(x) = \sum_{i = 0} ^ {n- 1} b_i \times x^i$ 在 $w_n^{ki}$

其中 $0 \le k < n$ 的点值

发现 $w_n^{-k}$ 与 $w_n ^ k$ 是对应的， $w_n^{-k} = w_n ^ {n + k}$

所以我们只要用 FFT 之后再将数组反过来就可以了

---

递归形式的 FFT

我们发现原来的每一个位置，在结束之后会变成其的反序

具体来说

```cpp
原来的序列: 000 001 010 011 100 101 110 111
结束的序列: 000 100 010 110 001 101 011 111
也就是二进制倒了
```

我们考虑求出最终的位置，之后再倒序回去，因为

```cpp
i 和 i / 2 的关系:
i = i >> 1 也就是二进制向左移动 1 位
既然是倒序,所以要向右移动 1 位
之后考虑奇数的情况,最后的一个 1 只要移动到最高位上就行了     
```

$Code$

```cpp
for(int i = 0; i < lim; ++ i) 
    rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
    
for(int i = 0; i < lim; ++ i) if(i < rev[i]) 
    swap(A[i], A[rev[i]]);
```

最后我们考虑蝴蝶操作过程

$m = \frac{n}{2}$ 的所有值已经计算好并且放到 A 中了
$$
\begin{aligned}
A_1(w_m^k) &= A[k], A_1(w_m^k) = A[k + m] \\\\
\Rightarrow A(w_m^k) &= A[k] + w_n^k \times A[k + m] \\\\
\Rightarrow A(w_m^{k + m}) &= A[k] - w_n^k \times A[k + m]
\end{aligned}
$$
之后我们一层层向上递推就可以了

---

## NTT

我们发现 FFT 的精度其实不是很够，最多处理 $10^8$ 的数据。我们考虑用什么东西来代替这个 FFT。这里有一个很好的替代品就是原根。对于质数 $p = qn + 1, n = 2^m$ 原根 $g$ 满足 $g^{qn} = 1 \pmod p$ 所以将 $g_n$ 看做 $\omega_n$ 也是可以的。

常用的模数是：
$$
p = 
\begin{cases}
998244353  = 479 \times 2 ^{21} + 1, g = 3\\\\
1004535809 = 7 \times 17 \times 2^{23} +1, g = 3\\\\
469762049 = 7 \times 2^{26} + 1, g = 3
\end{cases}
$$
因为他们的原根都是 3。(这里的模数都是常在 3% NTT 中使用的)

$g^{qn} = e^{2\pi n}$ 我们如果迭代到长度 $l$ 的时候 $\omega_n = g_l = g_n^{\frac{n}{l}} = g_n^{\frac{p - 1}{l}}$

----

## 3 模数 NTT

我们考虑如果模数不支持 NTT 怎么办？我们可以自己定模数，只要保证答案符合（大于）范围即可。

其实很简单，就是用 3 个模数做一遍 NTT 之后 CRT 合并即可，用的就是之前说的模数。

---

## MTT

我们考虑如果模数不支持 NTT 而且还卡常怎么办？~~虽然说 NTT 跑得很快~~，如果码力不行怎么办？用 MTT。

> 这里其实有点争议，就是  3% NTT 到底算不算 MTT。

本文只介绍一种最简单的，但是最常用的优化。

我们考虑进行 FFT 的时候我们用到了复数。那么我们为什么不把一个多项式的系数拆成几的部分分别进行处理，这样就可以保证精度。

假设我们要求 $P \times Q, P = \sum_{i = 0}p_ix^i, Q = \sum_{i = 1}q_ix^i$

我们考虑将系数拆成两部分

$ A = P_i >> 15, B = P_i \text{&} (32767) $

$C = Q_i >> 15, D = Q_i \text{&} (32767)$

$ P * Q = AC * 2 ^ {30}+(AD+BC)*2 ^ {15} + BD$

之后我们考虑通过复数来表示，设 $F = A + iB, G = C + i \times D$

这样我们可以先通过 FFT 之后再将其表示成答案的形式，最后在计算。

但是我们发现，需要表示之前的式子需要 $\overline{F}, \overline{G}$

> $\overline{F}$ 表示 F 的共轭复数。

那么我们来证明一个东西。我们不妨记 $conj(x)$ 表示 x 的共轭复数。

用 $P_t$ 表示 P 的 DFT。
$$
\text{证明：P = A + iB, Q = A - iB}\\\\
\color{red}\text{注意这里 P, Q 与之前的不同}
$$

$$
\begin{aligned}
p_t(x) =& A(w_n^k) + iB(w_n^k) \\\\
=& \sum_{j = 0} ^ {n - 1} A_jw_n^{jk} + i B_jw_n^{jk} \\\\
=& \sum_{j = 0} 6 {n - 1} (A_j + iB_j) w_n^{jk}\\\\
\end{aligned}
$$

之后我们考虑能不能将 Q 用 P 表示出来，我们考虑将 Q 进行展开。
$$
\begin{aligned}
Q_t(x) =& Aw_n^k - iB(w_n^k) \\\\
=& \sum_{j = 0} ^{n - 1} (A_j - iB_j) w_n^{jk} \\\\
=& \sum_{j = 0} ^ {n - 1} (A_j - iB_j)\Bigg(cos\bigg(\frac{2\pi jk}{n}\bigg) + isin\bigg(\frac{2\pi jk}{n}\bigg)\Bigg) \\\\
= & \sum_{j = 0} ^ {n - 1} A_jcos\bigg(\frac{2\pi jk}{n}\bigg)  + B_j sin\bigg(\frac{2\pi jk}{n}\bigg) + i(A_jsin\bigg(\frac{2\pi jk}{n}\bigg) - B_jcos\bigg(\frac{2\pi jk}{n}\bigg)) \\\\
= & conj(\sum_{j = 0} ^ {n - 1} A_jcos\bigg(\frac{-2\pi jk}{n}\bigg)  + B_j sin\bigg(\frac{-2\pi jk}{n}\bigg) - i(A_jsin\bigg(\frac{-2\pi jk}{n}\bigg) - B_jcos\bigg(\frac{-2\pi jk}{n}\bigg))) \\\\
= & conj(\sum_{j = 0} ^ {n - 1} (A_j + iB_j) \times (cos\bigg(\frac{-2\pi jk}{n}\bigg) - isin\bigg(\frac{-2\pi jk}{n}\bigg))) \\\\
= & conj(\sum_{j = 0} ^ {n - 1} (A_j + iB_j)(w_n^{-jk}) \\\\
    = & conj(\sum_{j = 0}^{n - 1} (A_j + iB_j)(w_n^{-jk})) \\\\
    = & conj(P_t(n - k))
\end{aligned}
$$
这里有两个细节，我们一开始是将两个式子都展开，之后通过欧拉定理进行展开，猜测两个数是共轭质数。

这里变成共轭的时候，将 cos 部分变成负数。

我们考虑一个更加普通的形式对于一个 $w_n^k$ 的共轭复数是多少？将 $w_n^k$ 表示成 $a + ib$ 的形式，其共轭复数容易得到是 $a -i b$ 之后我们考虑在数轴上对应的是多少

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-pQ3sobeh-1618049627378)(https://z3.ax1x.com/2021/04/10/cabTrq.png)]]

可以发现其在轴上的对应点就是 $w_n^{ -k}$ 可以理解成，原来是向上数 k 的单位，现在是向下数。

之后将其展开也是同理，可以得到 $w_n^{-k} = cos(\frac{-2\pi k}{n}) + isin(\frac{-2\pi k}{n})$

但是我们计算答案的时候可没有负的指数，我们可以用 $n - k$ 来表示 $-k$ 因为圆上总共有 n 个点。

这样我们就证明了 $Q$ 可以由 $P$ 表示出来。

所以我们总共只需要进行 4 次 FFT 就可以得到答案了。

---

## 多项式求逆

我们需要求 $F \times Q \equiv 1 \pmod {x^n}$ 中的 $Q$。

考虑使用递归解决，假设我们已经得到 $Q_1 \times F \equiv 1 \pmod {x^\frac{n}{2}}$ 

我们需要求 $F \times Q \equiv 1 \pmod {x^n}$

可以得到 $F \times Q \equiv 1 \pmod {x^{\frac{n}{2}}}$

考虑将两个式子进行相减来消除 1
$$
F \times (Q - Q_1) \equiv 0 \pmod {x^{\frac{n}{2}}}
$$
因为 $F$ 肯定不是 0，所以考虑将其消掉，之后再考虑平方。
$$
(Q - Q_1) ^ 2 \equiv 0\pmod {x^n} \\\\
Q^2 + Q_1^2 - 2QQ_1 \equiv 0 \pmod {x^n} \\\\
Q + FQ_1^2 - 2Q_1 \equiv 0 \pmod {x^n} ,\text{这里两边乘了 F}\\\\
Q \equiv 2Q_1 - FQ_1^2 \pmod {x^n}
$$
这样递归去做就可以了。

---

## 多项式 Ln

需要求 $Q \equiv \ln F \pmod {x^ n}$

我们考虑直接进行 exp ~~发现我们还没学过~~

那么能消去 Ln 的还有什么呢？ 求导！

$Q' \equiv \frac{F'}{F} \pmod {x^n}$

之后又手就行。

---

## 多项式 Exp

需要求 $Q \equiv e^F \pmod {x^n}$

我们优先考虑 Ln 转换成 $\ln Q \equiv F \pmod {x^n}$ 之后考虑进行牛顿迭代。
$$
\begin{aligned}
y &= f(x_0) + f'(x_0)(x - x_0) \\\\
0 &= f(x_0) + f'(x_0)(x - x_0) \\\\
0 &= f(x_0) - f'(x_0)x_0 + f'(x_0)\\\\
x &= \frac{f'(x_0)x_0 - f(x_0)}{f'(x_0)}\\\\
x &= x_0 - \frac{f(x_0)}{f'(x_0)}
\end{aligned}
$$
我们考虑将之前的式子变换得到 $\ln Q - F \equiv 0 $

我们设函数 $S(G) = \ln G - F$

然后考虑带入之前的式子

这里说明一下 $S'(G) = \frac{1}{G}$ 的原因，我们本质上是将整个 G 看成一个变量，而不是里面的每一个 $x^i$ 所以这里并不是符合函数求导。
$$
G = G_0 - \frac{\ln G_0 - F}{\frac{1}{G_0}} \\\\
G = G_0 - G_0 \times (\ln G_0 - F)\\\\
G = G_0 \times (1 - \ln G_0 + F)
$$
之后有手就行。

---

## 多项式开根

我们需要求 $F^2 = G$ 还是考虑递归，假设 $F_1^2  \equiv G \pmod {x^{\frac{n}{2}}}$

还是考虑消元 
$$
F \equiv F_1 \pmod {x^{\frac{n}{2}}}\\\\
F - F_1 \equiv 0 \pmod {x^{\frac{n}{2}}} \\\\
F^2 + F_1^2 - 2FF_1 \equiv 0 \pmod {x^n} \\\\
G  F_1^2 - 2F_1F \equiv 0 \pmod {x^n} \\\\
F \equiv \frac{G}{2F_1 + F_1^2} \pmod {x^n}
$$
还是很简单。

---

## 多项式除法

> 给定多项式 $F, G$ 求出多项式 $R, Q$ 使得 $F = G \times Q + R$。

> 其中 $F$ 的次数为 $n,G$ 的次数为 $m$。

可以想到如果没有余数的话，我们直接用多项式求逆就可以水掉。

因为这是个多项式，我们考虑将 R 给消掉，然后就可以与愉快得求逆了。

考虑构造 4 个多项式，满足 $F_1 = G_1 \times Q_1 + x^k R_1$ 这样子只要 $\mod x^k$ 就可以得到答案了。考虑构造这个多项式，为了让外面有 $x^k$ 这个系数，所以考虑将 $F(x)$ 变成 $F(\frac{1}{x})$。 可以知道变成这样之后的式子仍然是满足的，我们考虑两边同时乘以 $x^n$。

$x^nF(\frac{1}{x}) = x^{n}G \times Q + x^n R$ 我们考虑 $x^nF(\frac{1}{x})$ 的每一个系数其实等价于将原来的 F 给翻转一下的系数。那么我们不妨让 $F_1 = x^nF(\frac{1}{n}), Q_1 = x^mQ(\frac{1}{n})$ 其他同理。

那么我们得到 $F_1 = G_1 \times Q_1 + x^{n - m + 1}R_1$ 看出 $k = n - m + 1$。之后发现一个神奇的事情 $Q_1$ 的次数是 $n - m$ 所以我们即使 $\mod x^{n - m + 1}$ 也不会影响 $Q_1$。所以我们得到式子 $F_1(x) = G_1(x) \times Q_1(x) \mod x^{n - m + 1}$ 也就是 $Q_1(x) = \frac{F_1(x)}{G_1(x)}$。

之后我们求出了 $G_1$ 之后将其翻转一下就变成 $G$ 了，然后就可以得到 $R(x) = F(x) - G(x) \times Q(x)$。

很简单。

----

这边之后还会更新。


