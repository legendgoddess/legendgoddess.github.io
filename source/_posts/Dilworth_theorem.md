---
title: 最小链覆盖 Dilworth’s theorem
date: 2021-10-08 16:26:05
tags:
  - Dilworth
categories:
  - 算法
---

话说这个专题网上的讲解都没有很全，窝就将他们的总和一下。

> 如果有问题请私信，评论。

------

首先说一下最小链覆盖定理，这个本质上是有点抽象的。

$\color{red} \text{偏序集}$的概念：

我们设当前的全集为 $X$。也就是一个 $\color{red}\text{偏序集}$，因为其元素部分可以比较大小。

`链`对于 $X$ 的一个子集满足其是全序集，及其所有的元素可以比较大小。

`反链`对于 $X$ 的一个子集，满足其任意非空子集都不是全序集， 即所有的元素不能比较大小。

`链覆盖`若干个链的并集为 $X$，且两两交集为 $\empty$。

`反链覆盖`若干个反链的并集为 $X$，且两两交集为 $\empty$。

`最长链`所有链中元素最多的集合（可以有多个）。

`最长反链`所有反链中元素最多的集合（可以有多个）。

`偏序集高度`最长链的元素个数。

`最长反链`最长反链的元素个数。

------

狄尔沃斯定理($Dilworth’s theorem$)

最小链覆盖 $=$ 最长反链长度 $=$ 偏序集宽度

最小反链覆盖 $=$ 最长链长度 $=$ 偏序集高度

emmm，其实本质上对于链的定义可以靠自己，但是基本上按照题目的限制来。

------

$\color{red}Dilworth\text{ 定理在序列中的应用}$

[导弹拦截](https://www.luogu.com.cn/problem/P1020)

其实挺经典的，对于第一问不说。

对于第二问，考虑我们需要通过非严格下降子序列覆盖。

那么我们不妨定义链为非严格下降子序列。那么最小链覆盖就是最长的反链长度，也就是严格上升子序列的长度。

[CF1296E2 String Coloring (hard version)](https://www.luogu.com.cn/problem/CF1296E2)

> 给你一串长度为 $n$ 的字符串，你可以给每个位置上染上一种不大于 $n$ 的颜色。
>
> 对于相邻的两个位置，如果他们的颜色不同则可以交换他们的位置。现在需要交换若干次后按照字典序排序
>
> 你需要找到最少满足条件的颜色数并输出方案。

我们考虑对于一个不用交换的序列也就是非严格上升序列，我们不用用别的颜色。所以本题就是问最少非严格上升序列的个数，将其当做链。那么我们需要求的反链长度就是最长严格下降子序列的长度。

$\color{red}\text{某牛客题：}$

![5CWs8s.png](https://img-blog.csdnimg.cn/img_convert/a85eac0ece004db19834467d1ea02edb.png)

我们考虑对于纸飞机的飞行路径肯定是严格下降子序列，求其的最小覆盖，等价于求非严格最长上升子序列长度。

但是本题需要求除去一个数之后的答案，我们考虑对于同一个长度的答案是否有多种可能性。我们之前是维护以第 $i$ 个数结尾的长度，我们再记录一下最长序列第 $x$ 个位置的最大值，每次更新。

> 注意状态不能是 $f(i)$ 表示数 $i$ 结尾的最大长度，这会有重复，导致无法进行之后的 $Dp$。

对于同一个长度的序列有多个方案而且当前的数被选中了，那么不用减去 $1$ ，不然肯定需要去掉当前的数。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
#endif // Fread

template <typename T>
void r1(T &x) {
	x = 0;
	char c(getchar());
	int f(1);
	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
	x *= f;
}

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
int n;
const int maxn = 2e5 + 5;
struct Node {
    int t[maxn];
    inline int lowbit(const int &x) {
        return x & -x;
    }
    void add(int pos, const int &c) {
        for(; pos <= n; pos += lowbit(pos)) t[pos] = max(t[pos], c);
    }
    inline int ask(int pos) {
        int res(0);
        for(; pos > 0; pos -= lowbit(pos)) res = max(res, t[pos]);
        return res;
    }
}T;
int a[maxn], b[maxn];
int f[maxn];
int vis[maxn], mx[maxn], sum[maxn];

const int inf = 2e9;

signed main() {
//    freopen("data.in", "r", stdin);
//    freopen("S.out", "w", stdout);
	int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i]), b[i] = a[i];
    sort(b + 1, b + n + 1);
    int len = unique(b + 1, b + n + 1) - b - 1;
    for(i = 1; i <= n; ++ i) a[i] = lower_bound(b + 1, b + len + 1, a[i]) - b;
    int ans(0);
    for(i = 1; i <= n; ++ i) {
        f[i] = T.ask(a[i]) + 1;
        T.add(a[i], f[i]);
        ans = ans < f[i] ? f[i] : ans;
    }
//    puts("ZZZ");
    mx[ans + 1] = inf;
    for(i = n; i; -- i) {
        if(a[i] <= mx[f[i] + 1]) {
            // 判断能否产生贡献
            vis[i] = 1;
            ++ sum[f[i]];
            mx[f[i]] = max(mx[f[i]], a[i]);
        }
    }
    for(i = 1; i <= n; ++ i) {
        if(sum[f[i]] == 1 && vis[i]) printf("%d ", ans - 1);
        else printf("%d ", ans);
    }
	return 0;
}
/*
In:
8
15569 23023 23126 4942 13716 13350 24946 4856

Out:
3 3 3 4 4 4 3 4
*/
```

------

$\color{red}Dilworth\text{ 定理在 } DAG\text{ 中的应用}$

我们需要重新定义一下链和反链。

```链```该集合中任意两个点 $x, y$ ，满足要么 $x$ 可以到达 $y$ 要么 $y$ 可以到达 $x$。

```反链```该集合中任意两个点 $x, y$ ，满足 $x$ 不能到达 $y$ $\color{red}\text{而且}$ $y$ 不能到达 $x$。

$\color{red}\text{定理：}$

最长反链长度 $=$ 最小链覆盖

最长链长度 $=$ 最小反链覆盖

$\color{green}\text{那么我们要求出的就是这个有向无环图的最小链覆盖了。}$

$\color{green}\text{最小链覆盖即路径可以相交的最小路径覆盖。}$

$\color{red}DAG\text{ 的最小路径覆盖}$

$\color{red}\text{定义}$：在一个有向图中，找出最少的路径，使得这些路径经过了所有的点。

最小路径覆盖分为最小不相交路径覆盖和最小可相交路径覆盖。

最小不相交路径覆盖：每一条路径经过的顶点各不相同。如图，其最小路径覆盖数为 $3$ 。即 $1\to3\to4，2，5$ 。

最小可相交路径覆盖：每一条路径经过的顶点可以相同。如果其最小路径覆盖数为 $2$ 。即 $1\to3\to4，2\to3\to5$。

特别的，每个点自己也可以称为是路径覆盖，只不过路径的长度是0。

$\color{red}\text{定理：}$

$\tt Case\ 1:$ 最小不相交路径覆盖。

> $ans = n - \text{拆点二分图最大匹配数}$.

$\tt Case\ 2:$最小可相交路径覆盖。

> 我们先向图传递闭包，之后暴力建图之后求最大匹配。
>
> $ans = \text{二分图最大匹配}$。

传递闭包本质上就是 $Floryd$ 就是如果两个点可以间接或者直接到达，其在闭包上就有边。

```cpp
for(int i=1;i<=n;++i)
    for(int j=1;j<=n;++j)
        for(int k=1;k<=n;++k)
            dis[i][j] |= dis[i][k] & dis[k][j];
```

我们考虑进行 $bitset$ 优化。

```cpp
for(int i = 1; i <= n; ++ i)
    for(int j = 1; j <= n; ++ j) if(bt[j][i])
        bt[j] |= bt[i]; 
```

$\color{red}\text{注意：}$

对于使用哪一种路径，关键看题目中的要求，题目中说两个点不能相交那么就使用最小不相交路径。

$\color{red}\text{bzoj 1143}$

![g0ViGR.png](https://img-blog.csdnimg.cn/img_convert/8b87f9027061b6d25670171c260731f1.png)

本质上求最大有多少个节点不能相互影响，本质上就是求最大的反链。

就是最小链覆盖，发现题目中说不能相同，就直接最小不相交路径覆盖。

[POJ 1422 Air Raid](http://poj.org/problem?id=1422)

> 考虑一个小镇，那里的所有街道都是单向的，并且每条街道都从一个路口通向另一个路口。还众所周知，从一个十字路口开始，穿过城镇的街道，您将永远无法到达同一十字路口，即，城镇的街道没有周期。

> 基于这些假设，您的任务是编写一个程序，以找到可以降落在城镇上的伞兵的最小数量，并以一种以上的伞兵不去任何十字路口的方式访问该镇的所有十字路口。每个伞兵都会降落在一个十字路口，并可以沿着城镇街道访问其他十字路口。每个伞兵的起始十字路口都没有限制。  

显然求最大反链，就是最小链覆盖。

发现不能重复（一种以上的伞兵不去任何十字路口的方式访问该镇的所有十字路口），所以是最大不相交路径覆盖。

[POJ 2594 Treasure Exploration](http://poj.org/problem?id=2594)

> 一个星球上有n个点，给出几条路，机器人可以沿着路遍历，问要遍历完整个星球最少需要几个机器人。

> 注意每个点可以重复遍历。

注意可以重复遍历，直接最大可相交路径覆盖。
