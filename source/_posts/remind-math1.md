---
title: 数学回忆1
date: 2022-4-22 21:29:00
tags:
  - 数论
  - 反演
categories:
  - 算法
sticky: 1
---
### 加乘原理

对于给定的集合 $S$ 对于其中满足某一性质 $P$ 的元素 $x$ 求和 $f(x)$ 即求出：

$$
\sum_{x \in S} f(x) [P(x)]
$$

 将 $S$ 称为**组合类**，$x$ 称为**组合对象**，$f$ 称为**权值函数**。

- #### $\color{blue}\Delta$ 构造双射

1. 不同的两个组合类中的组合对象可以一一对应，这样对于两个组合类进行计数是等价的。
2. 另一种情况是同一组合类中的组合对象可以建立对应关系 ( 或多个为一组 )，我们只需要对于每组中的一个进行计数，再乘上组的大小。

- #### $\color{blue}\Delta$ **加法原理**

对 $S$ 中的所有元素求和 $f(x)$ 等价于将 $S$ 划分成若干个集合 $S_i$ 分别求和再相加：

$$
\sum_{x \in S} f(x) = \sum_{i = 1} ^ m (\sum_{x \in S_i} f(x))
$$

- #### $\color{blue}\Delta$ **乘法原理**

将 $S$ 拆分成 $S_i$ 的笛卡尔积， $S$ 中元素的权值 $f(x)$ 等于其拆分出的各 $f_i(x_i)$ 只积，则对 $S$ 中的所有元素求和 $f(x)$ 等价于对于每个 $S_i$ 进行 $f_i(x_i)$ 求和之后相乘。

$$
\sum_{x \in S} f(x) = \prod_{i = 1} ^ m (\sum_{x_i \in S_i} f_i(x_i))
$$

我们除了可以交换 $\prod, \sum$ 我们还可以交换多个 $\sum$：

$$
\sum_{x \in A} \sum_{y \in B} f(x, y) = \sum_{y \in B} \sum_{x \in A} f(x, y)
$$

这个常在 $B$ 受到 $x$ 的限制时的组合计数。

> 求 $n$ 原正整数组 $(x_1, \cdots, x_n)$ 的数量，满足：
>
> - $x_i \le a_i$
> - $x_i$ 互不相同

设 $S = [a_i], f(x_1, \cdots, x_n) = [x1 \ne x_2] \cdots [x_1 \ne x_n] \cdots [x_{n -1} \ne x_n]$。

那么本质就是对于 $\sum_{x_1 \in S_1, x_2 \in S_2 \cdots} f(x1, \cdots, x_n)$。

设 $f(x_1, \cdots, x_{i - 1} | x_i) = [x_i \ne x_1] \cdots [x_i \ne x_{i - 1}]$。

那么原式可以变成：

$$
\sum_{x_1 \in S1} \sum_{x_2 \in S_2} f(x_1 | x_2) \cdots \sum_{x_n \in S_n} f(x_1, \cdots, x_{n - 1} | x_n)
$$

显然 $x_i$ 受到了前面权值的限制，我们通过交换求和符号使得 $a_i \le a_{i + 1}$ 那么 $x_{i + 1}$ 的所有限制就是可以被描述出来的，对于 $x_i$ 恰好有 $a_i - i + 1$ 种方案：

$$
\prod_{i = 1} ^ m (\sum_{x_i \in S_i} f_i(x_i)) = \prod_{i = 1} ^ m (a_i - i + 1)
$$

> [AT3956 [AGC023E] Inversions](https://www.luogu.com.cn/problem/AT3956)

对于逆序对合并计算是比较复杂的，更何况加上 $P_i \le A_i$ 的限制，我们考虑对于 $i < j, P_i > P_j$ 的所有贡献。

$i < j, P_i > P_j$ 在前一例题的基础上我们只是多钦定了两个数的大小关系，对于 $A_i < A_j$ 我们可以看成 $A_j = A_i$ 的情况。

发现单独的方案限制很难描述，既然是 $A_j = A_i$ 而且是互不相同我们那么逆序的方案就是总方案的 $\frac{1}{2}$。

对于 $i < j, A_i > A_j$ 的情况我们发现顺序对是很好求的，我们使用总方案减去顺序对方案。

> [P7140 [THUPC2021 初赛] 区间矩阵乘法](https://www.luogu.com.cn/problem/P7140)

看成矩阵 $A \times B$ 的形式，有 $\sum_{i} \sum_j \sum_k A(i, k) \times B(k, j)$。

考虑将 $k$ 移到最前面，对于 $A$ 就是列和，$B$ 就是行和。

我们有 $\sum_k (\sum_i A(i, k))(\sum_{j} B(k, j))$ 。

发现题目保证合法，意味着 $p + d^2 \le n$ 所以 $d$ 是 $O(\sqrt n)$ 级别的。

> - 求给定的长度为 $n$ 的序列所有区间异或和的和。
> - 求给定的大小为 $n$ 的集合所有子集异或和的和。

枚举每一位。

- 区间异或和之和：对于位置 $r$ 只需要记录左边有多少个和当前值不同的。
- 子集异或和之和：明显子集个数太多信息不能保存，考虑构造双射，对于一个集合我们只关心其 $1$ 的个数。不妨考虑有 $x$ 个 $1$，对于任意位置选取和不选取是只是改变奇偶性，对于每个位置是独立的，所以奇和偶的方案是相同的。所以方案数是 $2^{n - 1}$。

> 给定一棵包含 $n$ 个结点的外向树，求其拓扑排序的个数。

可以变成有根数，考虑对于节点 $i$ 其必须在其所有子树的节点前面。

对于所有排列，$i$ 最先出现的概率是 $\frac{1}{siz_i}$，对于每个节点都是不会相互影响的，因为要么是无关要么是父亲已经出现过了，所以方案数是 $\frac{n!}{\prod siz_i}$。

这里启发了我们我们不一定是考虑同时出现在 `queue` 中的点数可以任意排列，可以转而考虑每个点的限制，或者每个点限制了什么。

> 给定 $n$ 个正整数 $d_{1, \cdots, n}$，求有多少个数列 $a_{1 \cdots, n}$ 满足：
>
> - $a_i | d_i$
> - $\prod_{i}a_i \le \prod_{i} \frac{d_i}{a_i}$

发现直接进行计算不好处理限制，先想构造双射。

对于限制 $1$ 如果 $a_i^2 \ne d_i$ 对于一个 $a_i$ 显然可以对应一个唯一的 $\frac{d_i}{a_i}$。换言之就是对于一个满足条件的肯定可以映射到一个不满足条件的。总方案数就是 $d$ 的因子个数之积。

之后考虑 $\prod a_i = \prod \frac{d_i}{a_i}$ 的情况。

移项，之后考虑每个质因子的出现次数是固定的，对于每个质因子分别进行背包。

### 组合数

- #### $\color{blue}\Delta$ $\tt lucas$ 定理

设 $n, m$ 的 $p$ 进制为 $a_k\dots a_0, b_k \dots b_0$ 那么

$$
\binom{n}{m} \equiv \prod_{i = 0} ^ k \binom{a_i}{b_i} \pmod p
$$

其中 $p$ 是质数，$n, m$ 是自然数。

**命题**：$\binom{n + m}{n} \equiv 0 \pmod p$ 当且仅当 $n, m$ 的加法在 $p$ 进制下进位。

> 根据上述定理可以知道如果 $a_i < b_i$ 就去世了，显然如果 $a + b$ 进位了那么剩下的部分肯定是小于 $a, b$ 的。

- #### $\color{blue}\Delta$ Kummer 定理

$\dfrac{(n + m)!}{n! m!}$ 中质因子 $p$ 的次数恰好为 $n + m$ 计算过程中在 $p$ 进制小进位的个数。

- #### $\color{blue}\Delta$ 扩展 $\tt lucas$

对于 $\dbinom{n}{m} = \dfrac{n!}{m!(n - m)!}$ ，考虑分成是 $p$ 的倍数和不是 $p$ 的倍数两部分进行计算。

对于 $p$ 拆成 $\prod_i p_i^{a_i}$ 其中 $p_i$ 是质数，之后 $\tt CRT$ 回去。

不妨考虑 $n!, m!,(n - m)!$  中 $p_i$ 的因子分别为 $u, v, w$。

那么有 $\dbinom{n}{m} = \dfrac{\dfrac{n!}{p_i^u}}{\dfrac{m!}{p_i^v}\dfrac{(n - m)!}{p_i^w}} \times p_i^{u - v - w}$。

对于 $u$ 的计算是容易的 $u = \sum_{i = 1} \dfrac{a_i}{p_i^i}$。

可以发现 $a! = p_i^{\frac{a}{p_i}} \times \frac{a}{p_i}! \times \prod_{i = 1, i \not \equiv 0 \pmod {p_i}} i$ 后面是一个周期函数可以预处理 $O(1)$ 计算。

左边的阶乘继续递归。

- #### $\color{blue}\Delta$ 组合数比大小

给定 $\dbinom{a}{b}, \dbinom{c}{d}$ 比较大小，直接取对数。

- #### $\color{blue}\Delta$ 组合数前缀和

有 $q$ 组询问，每次给定 $n, m$ 求出 $\sum_{i = 0} ^ m \dbinom{n}{i}$。

考虑使用莫队，设答案为 $f(n, m)$ 我们有 $f(n, m + 1) = f(n, m) + \binom{n}{m + 1}$，$f(n + 1, m) = 2f(n, m) - \binom{n}{m}$。

> [别人的代码](https://www.cnblogs.com/zwfymqz/p/9751173.html)

> [[AHOI 2017 / HNOI 2017] 抛硬币](https://www.luogu.com.cn/problem/P3726)

考虑对于 $a = b$ 的情况对于甲胜和乙胜的情况是双射。

只要算出平局的数量即可。

对于 $a > b$ 的情况，可能会出现反转和之前都是甲胜的情况。

考虑甲正面朝上 $i$ 次，乙 $j$ 次，我们有 $i > j, a - i > b - j$ 得到 $i - j \ge 1, a - b - 1 \ge i - j$。

考虑计数 $\dfrac{2^{a + b} + \sum_{u = 0} ^ b \sum_{v = 1} ^ {a - b - 1} \binom{b}{u}\binom{a}{u + v}}{2}$。

> $j, i - j$。

后面部分是个范德蒙德卷积等于 $\binom{a + b}{b + v}$。

如果 $a = b$ 的话需要特判。

> 求有多少组 $a_1, \cdots, a_k$ 满足：
>
> - $a_i \le x_i$
> - $\binom{\sum a_i}{a_1, \cdots, a_k}$ 是奇数

考虑 $\tt lucas$ 也就是不存在 $2$ 的倍数。

首先考虑 $\binom{\sum a_i}{a_1, \cdots, a_k}$ 是多项式系数，先转化 $\binom{\sum a_i}{a_1, \cdots, a_k} = \prod \binom{a_i + \cdots + a_k}{a_i}$。

也就是任意 $a_i$ 与为 $0$。

直接进行数位 $\tt Dp$。

### 容斥原理

$$
|\bigcup_{i = 1} ^ n S_i| = \sum_{\empty \ne T \subseteq [n]} (-1) ^ {|T| - 1} |\bigcap_{i \in T} S_i|
$$

其中 $[n]$  指 ${1,2,\cdots,n}$，这个式子的含义是，这些集合并集的大小可以通过枚举一个子集的集合，将它们求交并乘上 $−1$ 的子集大小次方求和得到。

一种理解是二项式定理：

$$
\sum_{i = 1} ^ n (-1) ^ i \binom{n}{i} = \sum_{i = 0} ^n \left((-1)^n \binom{n}{i}\right) - \binom{n}{0} = - 1
$$

所以 $s$ 的每个元素恰好被计算 $1$ 次。

> 考虑任意元素，其在大小为 $i$ 的集合的计算次数。

其还有一个常见形式：

$$
|\bigcup_{i = 1} ^ n S_i| = \sum_{T \subseteq [n]} (-1) ^ {|T|} |\bigcap_{i \in T} S_i|
$$

聪明的你会发现这个东西就是原来的补集，我们定义 $T = \emptyset$ 的时候后面的式子定义为全集大小。

我们可以直观地理解容斥原理：

- $n$ 件事至少发生一件的方案数，可以通过其中每个子集同时发生的方案计算；
- $n$ 件事都不发生的方案数，可以通过其中每个子集同时发生的方案计算。

> 全错排问题
>
> 求 $1, \ldots, n$ 的偶爱列 $p_1, \ldots, p_n$ 的数量，使得 $i \ne p_i$。

设 $S_i$ 为所有满足 $i = p_i$ 的集合，我们求的就是 $\bigcup_{i = 1} ^ n S_i$。

使用容斥原理考虑钦定了 $i$ 个 $p_i = i$ 的方案数，可以写出答案：

$$
|\bigcup_{i = 1} ^n S_i| = \sum_{i = 0} ^ n (-1) ^ i \binom{n}{i} (n - i) !
$$

我们甚至可以使用生成函数得到递推式，这个不提 $a_n = (n - 1) (a_{n - 1} + a_{n - 2})$。

> 求正整数列 $a_{1 \ldots n}$ 的数量，满足：
>
> - $a_i \le x_i$
> - $\sum a_i = S$

设 $S_i$ 是满足 $a_i > x_i$ 的集合，我们要求 $\bigcup_{i = 1} ^ n S_i$。

容斥原理，不妨考虑钦定 $a_i = x_i + c$。

我们直接使用隔板法即可。

> 给定 $n$ 个字符串 $T_1, \ldots, T_n$，字符串中有些问号表示可以填上任意小写字母，问所有填写方案中这些串构成的字典树结点数之和。

考虑 $S_i$ 是字典树上第 $i$ 个串的所有前缀对应结点构成的集合，我们求 $\bigcup_{i = 1} ^ nS_i$。

容斥原理，交集就是 $\tt Lcp$，可以通过枚举 $\tt lcp$ 长度算方案数。

> [[AGC 005 D] ~K Perm Counting](https://www.luogu.com.cn/problem/AT2062)
>
> 求 $1, \ldots, n$ 的排列 $p_1, \ldots, p_n$ 的数量，使得 $|i - p_i| \ne k$。

容斥，不讲。

> [[ARC 101 C] Ribbons on Tree](https://www.luogu.com.cn/problem/AT4352)
>
> 将给定的包含偶数 $n$ 个点的树上的点两两配对，使得每对点之间路径的并集包含了每一条树边，求方案数。

容斥枚举不合法的边集，考虑删除了 $E$ 之后树变成 $E + 1$ 个连通块，每个连通块都内部匹配，不妨考虑连通块大小为 $n$ 那么方案数就是 $(n - 1) !!$。

设 $f(i, j)$ 表示点 $i$ 子树中 $i$ 所在连通块大小为 $j$ 时的容斥系数。

$f(i, 0)$ 表示 $i$ 到父亲边没有被覆盖，特别计算。

### 二项式反演

问题：有 $n$ 个集合 $S_1,\ldots,S_n$，我们要求其中属于恰好某指定 $k$ 个集合的元素数量，并且任选 $k$ 个集合算出来的**答案是一样的**。

设答案为 $g_k$，考虑求出 $f_k$ 表示钦定某属于 $k$ 个集合的元素数量，有：

$$
f_k = \sum_{i = k} ^ n g_i \binom{i}{k}
$$

使用容斥推出 $g_k$：

$$
g_k = \sum_{S \in T} f_{|T|} \times (-1)^{|T| - k} = \sum_{|T| = k} ^ n f_{|T|} \times \binom{|T|}{k} \times (-1) ^ {|T| - k}
$$

二项式反演还有另一形式：

$$
\begin{aligned}
f_k &= \sum_{i = 0} ^k g_i \times \binom{k}{i} \\\\
g_k &= \sum_{i = 0} ^ k f_i \binom{k}{i} \times (-1) ^ {k - i}
\end{aligned}
$$

这里 $f_k$ 表示至多属于某指定 $k$ 个集合的元素数量。

我们还有：

$$
\begin{aligned}
f_k = \sum_{i = 0} ^ k g_i \times \binom{k}{i} \times (-1) ^ i \\\\
g_k = \sum_{i = 0} ^ k f_i \times \binom{k}{i} \times (-1) ^ i
\end{aligned}
$$

> [[CF 1228 E] Another Filling the Grid](https://www.luogu.com.cn/problem/CF1228E)

这种情况是二维的，具体讲一下二维怎么反演。

我们设 $f(a, b)$ 表示每一维钦定了 $a, b$ 是不符合条件的，$g(a, b)$ 是恰好。

我们有 ：

$$
f(a, b) = \sum_{i = a} ^ n \sum_{j =b} ^ n g(i, j) \times \binom{i}{a} \times \binom{j}{b}
$$

这个是显然的。

反演的时候我们考虑一维一维进行反演，设 $h(a, b)$ 表示 $a$ 是钦定，$b$ 是恰好，我们有。

$$
h(a, b) = \sum_{i = b} ^ n (-1) ^{i - b} \binom{i}{b} f(a, i)
$$

$$
g(a, b) = \sum_{i = a} ^ n (-1) ^ {i - 1} \binom{i}{a} h(i, b)
$$

所以综合一下有：

$$
g(a, b) = \sum_{i = a} ^n \sum_{j = b} ^ n (-1) ^ {i - a} (-1) ^{j - b} \binom{i}{a} \binom{j}{b} f(a, b)
$$

更高维度也是同理的，因为 $\sum$ 是可以分步反演的。

### 莫比如斯反演

> 只讲基础内容

自然的前缀和与差分之间就是反演，二项式反演可以看作一种“二项前缀和”和“二项差分”的转化，而莫比乌斯反演是 $Dirichlet$ 前缀和与 $Dirichlet$ 差分的转化。

设有数论函数 $g_i, f_i$ 如果满足：

$$
f_k = \sum_{d | k} g_d
$$

那么成 $f$ 是 $g$ 的 $\tt Dirichlet $ 前缀和，$g$ 是 $f$ 的 $\tt Dirichlet $ 差分。

我们可以找到很多数论中这种运算与多项式乘法之间的关系，例如 Dirichlet 前缀和实际上就是将一个函数 $g$ 与 $1$ 函数 $1(n) = 1$ 进行 Dirichlet 卷积（其实就是高维多项式卷积）的结果，一如前缀和可以看成一个函数与 $\frac{1}{x - 1}$ 的卷积，事实上 **Dirichlet 前缀和就是高维前缀和**。

设 $k$ 有 $c$ 个素因子 $p_1, \ldots, p_c$，$c$ 维函数 $F(a_1, \ldots, a_c)$ 的前缀和，其差分为：

$$
G(a_1, \ldots, a_c) = \sum_{S \in [c]} F(a_1 - [1 \in S], \ldots, a_c - [c \in S]) (-1) ^ {|S|}
$$

在数论背景下，这个就是 $\mu(k)$。

所以我们有 $g_k = \sum_{d | k} f_d \times \mu(\frac{k}{d})$。

这就是**莫比乌斯反演**，这和上面的高维差分是等价的，这告诉我们数论函数的 Dirichlet 差分由它与莫比乌斯函数卷积得到。

对于后缀和也有类似的反演形式：

$$
f_k = \sum_{d | k} g_d
$$

$$
g_k = \sum_{d | k} f_d \times \mu(\frac{d}{k})
$$

还有 $\varphi = \mu \times id, id = \varphi \times 1, \mu \times 1 = \epsilon$，其中 $\epsilon(n) = [n = 1]$。

我们通常将 $\gcd(a, b) = d$ 转化成 $d | \gcd(a, b)$。

### Min - Max 容斥

Min-Max 容斥是一种将集合最大值用其子集最小值表示的方法，当然由于相对全体实数来说，Min 和 Max 这两种运算是对称的，所以反过来也成立。

设有数集 $S$ 令 $\max(S), \min(S)$ 分别表示其中最大值和最小值那么有：

$$
\max(S) = \sum_{\emptyset \ne T \subseteq S} (-1) ^{|T| - 1} \min(T)
$$

设 $S = \{S_1, \ldots, S_n\}$，令 $T_i$ 表示不大于 $S_i$ 的正整数集合，则 $\max(S) = |\bigcup T_i|, \min(S) = |\bigcap T_i|$。就是基础的容斥原理。

由于 $\max(S) = - \min(-S)$。

我们也有：

$$
\min(S) = \sum_{\emptyset \ne T \subseteq S} (-1) ^{|T| - 1} \max(T)
$$

到这里，观察普通的容斥和 Min-Max 容斥，我们已经注意到了：

- 针对集合的普通容斥中，**包含**是定义在集合上的偏序关系，而交并分别是取下界和上界；
- 针对实数的 Min-Max 容斥中，**不大于**是定义在实数集合上的偏序关系，而 ⁡$\min,\max$ 分别是取下界和上界。

由整数的整除偏序可以导出 **GCD-LCM 容斥**：

$$
\gcd(S) = \prod_{\emptyset \ne T \subseteq S} (lcm(T)) ^ {(-1) ^{ |T| - 1}}
$$

$$
lcm(S) = \prod_{\emptyset \ne T \subseteq S} (\gcd(T)) ^ {(-1) ^{ |T| - 1}}
$$

考虑如何使用子集的 $\tt min$ 表示出一个集合的第 $k$ 大数:

$$
\max_k(S) = \sum_{T \subseteq S, |T| \ge k} (-1) ^ {|T| - k} \times \binom{|T| - 1}{k - 1} \times \min(T)
$$

证明同样考虑贡献，由于 $|T| \ge k$，所以比第 $k$ 大数更大的数一定不会再右边出现。

然后考虑第 $l > k$ 大的数的贡献，它是：

$$
\sum_{i = k} ^ l (-1) ^ {i - k} \times \binom{i - 1}{k - 1} \times \binom{l - 1}{i - 1} = \binom{l - 1}{k - 1} \sum_{i = k} ^ l (-1) ^ {i - k} \binom[l - k]{i - k}
$$
后者只有 $l = k$ 时才为 $1$。

Min-Max 容斥的一个常见用途也是将“完全”转化为“至少”，假设有 $n$ 个事件，我们需要计算它们全部发生的期望时间，可以转化为计算某个子集至少发生一件的期望时间。

> [P4707 重返现世](https://www.luogu.com.cn/problem/P4707)
>
> 有 $n$ 种原料，美妙随机生成一种，生成第 $i$ 中的概率为 $\dfrac{p_i}{m} (m = \sum p_i)$，问第一次集齐 $k$ 中原料的期望时间。

考虑选取集合需要进行 $\tt Dp$ 设 $f(i, j, k)$ 表示考虑了前 $i$ 个数选择了 $j$ 个 $\sum p = k$ 的方案数。
$$
f(i, j, k) = f(i - 1, j, k) + f(i - 1, j - 1,p - p_i)
$$
复杂度是 $O(n^2m)$ 的。

下面就需要优化了，一般这种 DP 的优化思路就是**将一部分系数通过组合意义或组合恒等式化到 DP 值中去**，这里我们就进行这样的尝试。

首先这个 $k$ 不妨变成收集了 $k$ 个材料中任意一个的最小时间。

那么不妨假设集合为 $P$ 没有收集到该集合的概率为 $u = \frac{\sum_{x \not \in P} p_x}{m}$。

所以我们期望时间为 $\frac{1}{1 - u}$。

考虑 $n -k = 10$ 的限制，我们不妨考虑求第 $k$ 大。

那么 $q = n - k + 1$。

之后考虑对于相同的 $\sum p$ 我们的 $\min(T)$ 本质是一样的，所以我们只需要求前面部分的系数和。

如果不选转移比较显然，考虑选择的情况：
$$
\sum_{T \subseteq S, |T| \ge q} (-1) ^ {|T| - q} \times \binom{|T| - 1}{q - 1}
$$
考虑拆开得到 $\binom{|T| - 1}{q - 1} = \binom{|T| - 2}{q - 2} + \binom{|T| - 2}{q - 1}$，发现我们只需要知道 $q$ 就可以在转移了。

考虑更改状态设 $f(i, j, p)$ 表示考虑前 $i$ 个，$q = j, \sum p_i = p$ 的系数和。

发现左边部分就是 $f(i - 1, j - 1, p - p_i)$ 对于右边部分是 $f(i - 1, j, p - p_i)$，考虑配一下系数。

前者是 $-$ 后者是 $+$。

所以 $f(i, j, p) = f(i - 1, k, p) + f(i - 1, j, p - p_i) - f(i - 1, j - 1, p - p_i)$。

> [P3175 [HAOI2015]按位或](https://www.luogu.com.cn/problem/P3175)
>
> 有一个初始为 $0$ 的数 $x$，美妙按照数 $i$ 权重 $p_i$ 来随机选择一个 $[0, 2^n - 1]$ 中的数，然后将 $x$ 或上这个数，求第一次 $x$ 变为 $2^n - 1$ 的期望时间。

考虑 $x$ 二进制第 $i$ 个 $1$ 出现时间为 $t_i$ 我们要求 $\max(t_i)$ 的期望， 就是需要求子集 $T$，$\min(T)$ 的期望。

同理可得期望时间为：
$$
\frac{1}{1 - \sum_{x \not \in T} p_x}
$$
需要计算子集和使用 $\tt FWT$。

> [P5644 [PKUWC2018]猎人杀](https://www.luogu.com.cn/problem/P5644)
>
> 按如下规则构造 $1, \ldots, n$ 的排列，每次从未被选择的元素中以第 $i$ 个元素 $p_i$ 的权重随机选择一个，求 $1$ 是最后一个的概率。 

发现直接计算不方便，考虑容斥设 $1$ 之后钦定去世的点集合为 $S$。

不妨考虑这个概率为 $p(S)$ 那么我们的答案就是 $\sum_S p(S) (-1) ^{|S|}$。

设 $sum(S) = \sum_{i \in S} a_i$ 考虑如何求 $p(S)$。

因为至少包含点集 $S$ 内的所有人考虑之前 $\tt uoj$ 喂鸽子的套路，考虑无限开枪，我们有：
$$
p(S) = \sum_{i\ ge 0} \left(\frac{tot - a_1  - sum(S)}{tot}\right)^ i \frac{a_1}{tot} = \frac{a_1}{a_1 + sum(S)}
$$
那么有：
$$
ans = \sum (-1) ^ {|S|} \frac{a_1}{a_1 + sum(S)}
$$
题目中给出 $\sum w_i = 10^5$ 会想到生成函数。

考虑对于同一个 $sum(S)$ 计算其系数设为 $f(sum(S))$。
$$
ans = \sum_{i = 0} ^ {tot} f(i) \frac{a_1}{a_1 + i}
$$
考虑每个向是否可以选择显然是 $\prod_{i = 2} ^ n (1 - x^{w_i})$。

使用分治 $\tt NTT$ 求出。

### 生成函数

很入门的东西已经写过很多了，就不谈了。

- #### $\color{blue}\Delta$ 牛顿迭代

根据群论我们知道 $\ge 5$ 次的方程是没有根式解的，我们考虑如何逼近求出这个解。不妨设 $r$ 是 $f(x) = 0$ 的一个解。

选取 $x_0$ 作为初始近似值，我们可以过点 $(x_0, f(x_0))$ 做曲线 $y = f(x)$ 的切线 $L$ 我们知道切点与 $x$ 轴有交点。

$L:y = f(x_0) + (x - x_0)f'(x_0)$ 其与 $x$ 的交点为 $x_1 = x_0 - \frac{f(x_0)}{f'(x_0)}$。

那么我们就有了迭代公式。

逝一逝：

> 多项式 $\exp$。
>
> 给定 $F(x)$，求满足 $\exp(F(x)) \equiv G(x) \pmod {x^n}$ 的 $G(x)$。

$$
\begin{aligned}
\exp(F(x)) &\equiv G(x) \\\\
F(x) &\equiv \ln G(x) \\\\
\ln G(x) - F(x) &\equiv 0
\end{aligned}
$$

考虑通过牛顿迭代求出 $G(x)$ 不妨设 $H(x) = \ln x - F(x)$。

带入上述公式得到：
$$
G_1(x) = G_0(x) - \frac{\ln G_0(x) - F(x)}{\frac{G_0'(x)}{G_0(x)}} = \frac{G_0(x) (1 - \ln G_0(x) + F(x))}{G_0'(x)}
$$
因为 $a_0 = 0$，发现对于 $n = 1$ 的情况我们是可以直接得到答案 $g_0 = 1$，之后通过迭代来增加幂次即可。

- #### $\color{blue}\Delta$ 欧拉变换

设一个 $ogf$ 是 $A(x)$ 的**无标号**组合类 $S$，其中大小为 $i$ 的对象有 $a_i$ 个。

- 每个对象是 $S$ 中若干个对象的组成的多重集。

我们称这个操作为欧拉变换，记作 $\mathcal E(A(x))$，这个东西就是一个完全背包考虑直接进行 $\tt Dp$ 本质上就是给答案乘上 $(1 + x^i +x^{2i}\cdots)$。
$$
\mathcal E(A(x)) = \prod_{i = 1} \frac{1}{(1 - x^i)^{a_i}}
$$

- 每个对象是 $S$ 若干个对象的不可重集。

$$
C(x) = \prod_{i = 1} (1 +x^i) ^{a_i}
$$

如何计算 $\mathcal E(A(x))$，乘积式的常见处理手法是化乘为加。
$$
\begin{aligned}
\mathcal E(A(x)) &= \exp(\ln \prod_{i= 1} (1 - x^i) ^ {-a_i}) \\\\
&= \exp(- \sum_{i = 1} a_i \sum_{i | j} - \frac{i}{j}x^j) \\\\
&= \exp(\sum_{i = 1} a_i \sum_{j = 1}\frac{x^{ij}}{j})
\end{aligned}
$$
复合的计算需要使用拉反之类的。

> [P4389 付公主的背包](https://www.luogu.com.cn/problem/P4389)
>
> 有 $n$ 个物品，第 $i$ 个大小为 $v_i$，有无限建，对每个 $i \in [1, m]$，求选择一些物品大小和为 $i$ 的方案数，不区分放入物品的顺序。

物品无标号，组合无标号，就是 $\mathcal E(x)$。

> [P5748 集合划分计数](https://www.luogu.com.cn/problem/P5748)
>
> 求出 $\tt bell(n)$ 表示将集合 $\{1, 2, \ldots, n\}$ 划分为若干个无标号非空子集的方案数。

集合是 $\{1, 2, \ldots, n\}$ 本身是有标号的，划分成无标号子集，组合是无标号的。

使用 $\tt egf$ 做 $\exp$，我们只需要做出每个非空子集的生成函数就是 $e^x - 1$。

> [CF438E The Child and Binary Tree](https://www.luogu.com.cn/problem/CF438E)
>
> 求有多少点带权二叉树，点权在给定集合 $S$ 中（$|S| = n$），且所有点的点权和为 $m$，二叉树点无标号，但区分左右儿子。

二叉树大小为点权和，设 $\tt ogf$ 是 $F(x)$，集合的生成函数是 $G(x)$。

$F(x) = G(x)F^2(x) + 1$。这里 $+1$ 表示递归 $f_0$ 的时候，也就是点权为 $0$ 的树的方案数。
$$
F(x) = \frac{1 \pm \sqrt{1 - 4G(x)}}{2G(x)}
$$
对于 $f_0 = 1$ 我们带入 $x = 0$ 观察是否复合答案。

使用分母无理化：
$$
F(x) = \frac{2G(x)}{1 \pm \sqrt{1 - 4G(x)}}
$$
显然下面取 $+$ 所以上面取 $-$。

> [十二重计数法 X](https://www.luogu.com.cn/problem/P5824)
>
> 将 $n$ 划分成 $m$ 个无序自然数之和的方案数。 

考虑最终划分之后的数，因为 $n = \sum_{i \ge 1} [n \ge i]$，设 $t_x$ 表示划分之后 $\ge x$ 的数个数。

> 因为每个数 $a$ 会对 $1 \sim a$ 做贡献，根据 $n$ 的那个式子拆分。

因为 $t_x \in [1, m]$ 本质上就是背包。
$$
[x^n] \prod_{i = 1} ^ m \frac{1}{1 - x^i}
$$

