---
title: 数据结构回忆3
date: 2022-4-5 21:09:00
tags:
  - 分块
categories:
  - 专题
sticky: 1
---

[Legendgod's Blog](https://legendgod.ml/)

---

#### 分块

> 一点都不会，从零开始学习。

- 区间分块：对于整个序列分成 $\frac{n}{B}$ 块，每块的大小为 $B$。

- 值域分块：同上，只是将值域分块了

- 询问分块，有的时候修改很少考虑记录修改对于之后每次询问暴力操作。

分块主要是维护信息不能高效合并的情况。

之后参考：[command_block 的博客](https://www.luogu.com.cn/blog/command-block/fen-kuai-xiang-guan-za-tan) 进行学习。

##### **题意**：[【模板】线段树 1 - 洛谷](https://www.luogu.com.cn/problem/P3372)

- 区间加，区间求和。

考虑序列分块。

- 询问：每块记录和，散块暴力询问。

- 修改：整块打标记，散块暴力修改。

> 可以暴力散块的时候清空整个散块的标记，或者直接标记永久化。

##### **题意**：[教主的魔法 - 洛谷](https://www.luogu.com.cn/problem/P2801)

区间加，查询区间 $\ge k$ 的数个数。

首先对于每块内部进行排序。

- 询问：二分位置，散块暴力。$O(\frac{n}{B}\log B + B)$。

- 修改：整块打标记，散块暴力归并排序。 $O(\frac{n}{B} + B)$。

取 $B = \sqrt{n \log n}$ 最优。

> 考虑上面的部分复杂度要大一点，所以考虑让上面部分平衡，可以根据均值不等式得到：$\frac{B^2}{\log B} = n$，我们把 $\log B$ 近似成 $\log n$ 可以得到 $B = \sqrt{n \log n}$。 

> 复杂度分析技巧 : 分块的主要思想是平衡，若复杂度由两部分组成，直接令两者相等，即可解得最优块大小。

##### **题意** : [LOJ #6279. 数列分块入门 3](https://loj.ac/p/6279)

- 区间加，查询区间前驱。

- 修改：整块打标记，散块暴力归并。 $O(\frac{n}{B} + B)$。

- 询问：整块暴力二分，散块暴力。 $O(\frac{n}{B} \log B + B)$。

复杂度同上。

##### **题意** : [[Ynoi2017] 由乃打扑克 - 洛谷](https://www.luogu.com.cn/problem/P5356)

- 区间加，区间第 $k$ 大。

发现值域很小是不是可以搞事情。

- 修改：整块打标记，散块归并。 $O(\frac{n}{B} +B)$。

- 询问：整体二分答案，散块暴力。 $O(\log w(\frac{n}{B} + B))$。

复杂度是 $O(q \sqrt n \log w)$ 卡卡能过。

> 假了，每次加数的值域是 $[-2\times 10^4, 2 \times 10^4]$。
> 
> 实际上数的值域是 $\tt int$ 范围内的。

- 修改：整块打标记，散块归并。 $O(\frac{n}{B} + B)$。

- 询问：二分答案，整块二分，散块暴力。 $O(\log w (\frac{n}{B}\log B + B))$。

取 $B = \sqrt{n \log n}$。

复杂度是 $O(q \log w \sqrt{n \log n})$，过不去。

考虑散块暴力能否优化，两边散块归并二分，散块的复杂度是 $O(\log n \log B)$ 的。

询问的复杂度是 $O(\frac{n}{B} \log B \log w)$，发现询问的复杂度可能会远高于修改，需要平衡：

$$
\begin{aligned}
\frac{n}{B} \log^ 2 n &= B \\\\
B &= \sqrt{n}\log n
\end{aligned}
$$

所以复杂度就是 $O(q \sqrt n \log n)$。

##### **题意：** [【模板】普通平衡树 - 洛谷](https://www.luogu.com.cn/problem/P3369)

1. 查询 $k$ 在区间内的排名。

2. 查询区间内排名为 $k$ 的值。

3. 修改某一位值上的数值。

4. 查询 $k$ 在区间内的前驱（前驱定义为严格小于 $x$，且最大的数，**若不存在输出 `-2147483647`**）。

5. 查询 $k$ 在区间内的后继（后继定义为严格大于 $x$，且最小的数，**若不存在输出 `2147483647`**）。

`1:` 前驱后继可以按照之前的做法是 $O(B + \frac{n}{B} \log B)$ 的。

`2:` 单点修改是 $O(B)$ 的。

`3:` 查询第 $k$ 大是 $O(\frac{n}{B} \log n \log V)$ 的。

`4:` 查询小于 $k$ 的数是 $O(B + \frac{n}{B}\log B)$ 的。

考虑让 `3, 4` 平衡可以得到：

$$
\begin{aligned}
\frac{n}{B} \log^2n &= B \\\\
B &= \sqrt{n} \log n
\end{aligned}
$$

复杂度还是 $q\sqrt n \log n$ 的。

> 貌似可过。

##### **题意** : [[HNOI2010]弹飞绵羊 - 洛谷](https://www.luogu.com.cn/problem/P3203)

维护一个 $n$ 个点的有向图，每个点都有且仅有一条出边，且指向比自己编号大的点。

支持修改某个点的出边，查询从某个点出发几步会到达 $n$ 号点。

> 这个题之前不是要用 $\tt LCT$ 的吗？

先考虑最初始的时候是可以直接计算出来的，然后考虑因为每个点只有一条出边，所以当一个点的出边被修改了之后，直接或者间接指向他的所有点的距离都要被修改。

询问就是单点查询。

> 感觉之后肯定是要平衡一下复杂度的。

发现每个点都是向后连边的，考虑序列分块，对于每个点记录跳出该块的步数，和到达的点。

查询复杂度是 $O(\frac{n}{B} + B)$，修改复杂度是 $O(B)$ 的。

总复杂度是 $O(q\sqrt n)$ 的。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
    namespace Read {
//        #define Fread
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
            for(; isdigit(c); c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
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
int n, m, p[maxn], B, bl[maxn], L[maxn], R[maxn];
int to[maxn], f[maxn], Bx;

void Rebuild(int id) {
    for(int i = R[id]; i >= L[id]; -- i) {
        if(i + p[i] > R[id]) f[i] = 1, to[i] = i + p[i];
        else f[i] = f[i + p[i]] + 1, to[i] = to[i + p[i]];
    }
}

int Query(int x) {
    int res(0);
    while(x <= n) {
        res += f[x], x = to[x];
//        printf("x = %d\n", x);
    }
    return res;
}

signed main() {
    int i, j;
    r1(n);
    B = sqrt(n) + 1;
    for(i = 1; i <= n; ++ i) r1(p[i]);
    for(i = 1; i <= n; ++ i) bl[i] = (i - 1) / B + 1;
    Bx = bl[n];
    for(i = 1; i <= Bx; ++ i) L[i] = R[i - 1] + 1, R[i] = i * B;
    R[Bx] = n;
    for(i = 1; i <= Bx; ++ i) Rebuild(i);
    int Q; r1(Q);
    while(Q --) {
        int opt, u, v;
        r1(opt, u), ++ u;
        if(opt == 1) printf("%d\n", Query(u));
        else { r1(v); p[u] = v; Rebuild(bl[u]); }
    }
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }//
```

##### **题意：** [LOJ #6546. 简单的数列题](https://loj.ac/p/6546)

给定两个长度为 $n$ 的数列 $A, B$ 支持下列操作：

- 数列 $A$ 区间加一个正数。

- 数列 $B$ 单点交换。

- 求 $\max_{i \in [l, r]} \{A_i \times B_i\}$。

考虑最优答案 `ans = add[i] * b[i] + a[i] * b[i]`，也就是 `-a[i] * b[i] = add[i] * b[i] - ans`，考虑每个点 $(b_i, a_i\times b_i)$ 本质上就是考虑 `k = add[i]` 的直线截得的最小截距。

考虑 $k$ 是逐渐增大的，所以我们维护的是下凸包，需要先按照 $x$ 进行排序维护。

`1.` 散块暴力重构，整块移动最优解。$O(B + \frac{n}{B})$。

`2.` 涉及到的块暴力重构。$O(B)$。

`3.` 整块直接算，散块暴力。$(B + \frac{n}{B})$。

复杂度是 $O(q \sqrt n)$ 的。

> 需要使用归并排序。

写这种题目的时候最好都先想清楚，之后再下笔开始写，最好在草稿纸上推演一遍，看看每个地方细节有没有可以改进的地方。

哦，我没有使用归并排序，所以复杂度是重构的时候为 $O(B \log B)$ 的。

让 $B = \sqrt{\frac{n}{\log n}}$ 最优。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
    namespace Read {
//        #define Fread
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
            for(; isdigit(c); c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
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
typedef long long int64;
const int maxm = 2e3 + 5;
struct Node {
    int64 x, y;
    int operator < (const Node& z) const {
        return x == z.x ? y < z.y : x < z.x;
    }
    Node operator - (const Node& z) const { return (Node) {x - z.x, y - z.y}; }
}bt[maxm][maxm], tmp[maxn];
int n, m, B, Bx, bl[maxn], L[maxn], R[maxn], ln[maxm], rs[maxm];
int64 a[maxn], b[maxn], mx[maxm], ad[maxm];

int check(Node A, Node B, Node C) {
    Node tmp1 = B - A, tmp2 = B - C;
    if(tmp1.y * tmp2.x - tmp1.x * tmp2.y <= 0) return true;
    else return false;
}

void rebuild(int id) {
    int tot(0), i;
    for(i = L[id]; i <= R[id]; ++ i) a[i] += ad[id];
    ad[id] = 0;
    for(i = L[id]; i <= R[id]; ++ i) tmp[++ tot] = { b[i], - a[i] * b[i] };
    sort(tmp + 1, tmp + tot + 1);
    ln[id] = 0, rs[id] = 1;
    for(i = 1; i <= tot; ++ i) {
        while(ln[id] > 1 && check(bt[id][ln[id] - 1], bt[id][ln[id]], tmp[i])) -- ln[id];
        bt[id][++ ln[id]] = tmp[i];
    }
    while(rs[id] < ln[id] && bt[id][rs[id]].y >= bt[id][rs[id] + 1].y) ++ rs[id];
    mx[id] = - bt[id][rs[id]].y;
//    printf("%d : %lld\n", id, mx[id]);
}

int64 Vb(int64 K, Node A) {
    return A.x * K - A.y;
}

void Change(int l,int r,int c) {
    if(bl[l] == bl[r]) {
        for(int i = l; i <= r; ++ i) a[i] += c;
        rebuild(bl[l]);
        return ;
    }
    for(int i = l; i <= R[bl[l]]; ++ i) a[i] += c;
    for(int i = L[bl[r]]; i <= r; ++ i) a[i] += c;
    rebuild(bl[l]), rebuild(bl[r]);
//    for(int i = L[bl[l] + 1]; i <= R[bl[r] - 1]; ++ i) a[i] += c, rebuild(bl[i]);
    for(int i = bl[l] + 1; i <= bl[r] - 1; ++ i) {
        ad[i] += c;
        while(rs[i] < ln[i] && Vb(ad[i], bt[i][rs[i]]) <= Vb(ad[i], bt[i][rs[i] + 1])) ++ rs[i];
        mx[i] = Vb(ad[i], bt[i][rs[i]]);
    }
}

void Swap(int l,int r) {
    swap(b[l], b[r]);
    if(bl[l] == bl[r]) rebuild(bl[l]);
    else rebuild(bl[l]), rebuild(bl[r]);
}

int64 Query(int l,int r) {
    int64 res(0);
    if(bl[l] == bl[r]) {
        rebuild(bl[l]);
        for(int i = l; i <= r; ++ i) res = max(res, a[i] * b[i]);
        return res;
    }
    rebuild(bl[l]), rebuild(bl[r]);
    for(int i = l; i <= R[bl[l]]; ++ i) res = max(res, a[i] * b[i]);
    for(int i = L[bl[r]]; i <= r; ++ i) res = max(res, a[i] * b[i]);
    for(int i = bl[l] + 1; i <= bl[r] - 1; ++ i) res = max(res, mx[i]);
    return res;
}

signed main() {
//    freopen("p1.in", "r", stdin);
//    freopen("pp1.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= n; ++ i) r1(b[i]);
    B = sqrt(n / 16) + 1;
    for(i = 1; i <= n; ++ i) bl[i] = (i - 1) / B + 1;
    Bx = bl[n];
    for(i = 1; i <= Bx; ++ i) L[i] = R[i - 1] + 1, R[i] = i * B;
    R[bl[n]] = n;
//    puts("SSS");
    for(i = 1; i <= Bx; ++ i) rebuild(i);
    while(m --) {
        int opt, l, r, c;
        r1(opt, l, r);
        if(opt == 1) r1(c), Change(l, r, c);
        else if(opt == 2) Swap(l, r);
        else printf("%lld\n", Query(l, r));
    }
    return 0;
}
/*
5 3
1 2 4 5 9
5 2 3 2 1
1 2 3 3
2 3 4
3 2 5
*/
}


signed main() { return Legendgod::main(), 0; }//
```

##### **题意** : [作诗 - 洛谷](https://www.luogu.com.cn/problem/P4135)

- 查询区间内出现偶数次的数（称为好数）的个数，强制在线。值域在 $O(n)$ 级别。

之前 $\tt rfy$ 跟我说了一个很妙的假做法 $\cdots$

首先第一想法就是使用莫队，而且是在线的，所以尝试考虑一下分块。

我们先说说需要记录一些什么东西，发现值域是很小的所以会有考虑记录每个数出现位置或者出现次数。

发现出现位置实际上没有什么作用，所以我们考虑记录出现次数。不妨设 $f(i, j)$ 表示在块 $i$ 中 $j$ 的出现次数。

我们考虑计算区间 $[l, r]$ 的时候好像不能很方便得计算，我们尝试对于每个块记录答案，设 $g(i, j)$ 表示在块 $[i, j]$ 之间的答案，之后我们再考虑散块的贡献。

对于整块我们需要每个数后缀出现次数，我们就将 $f(i, j)$ 改成这个定义，复杂度是 $O(\frac{n^2}{B})$。

之后考虑计算 $g(i, j)$。可以考虑向后枚举所有的数，动态记录答案对于每个块进行更新，复杂度是 $O(\frac{n}{B}\times n)$。

询问的时候考虑整块直接算答案，考虑散块单独处理，总共有 $O(B)$ 个值，然后进行分类讨论即可：

- 在区间中没有出现过。

- 出现过奇数次。

- 出现过偶数次。

考虑平衡上面的复杂度：

$$
\begin{aligned}
\frac{n^2}{B} &= nB \\\\
B &= \sqrt{n}
\end{aligned}
$$

##### **题意** : [[Violet]蒲公英 - 洛谷](https://www.luogu.com.cn/problem/P4168)

- 区间众数，强制在线。

注意到没有修改，所以明显需要在预处理上考虑。

发现 $a_i$ 的值域其实是骗人的，离散化之后一样是 $O(n)$ 的。

考虑同样是可以记录区间的答案，和每个数在每个块的出现次数，复杂度同上为 $O(\frac{n^2}{B})$。

考虑询问的时候对于整块找到答案，然后对于散块考虑每个数的出现次数暴力进行比较即可，是 $O(B)$ 的。

取 $B = \sqrt n$ 最优，为 $O(n\sqrt n + q \sqrt n)$。

**题意** : 区间众数，强制在线，要求空间 $O(n)$。

> [[Ynoi2019 模拟赛] Yuno loves sqrt technology III - 洛谷](https://www.luogu.com.cn/problem/P5048)

> 貌似是一道神题。

$n = 5 \times 10^5$，比较恐怖，当然我们还是可以离散化不用管他。

如果我们套用之前的做法发现空间是 $O(\frac{n^2}{B})$ 的。

考虑记录每个数出现的块位置，之前的部分还是照样，我们只需要考虑每个数在当前块出现的第一个位置。查询的时候找到每个数的右端点之后还需要二分左端点，复杂度是 $O(B\log n)$ 的。

考虑取 $B = \frac{n}{\sqrt {\log n}}$。发现空间是 $n\sqrt{\log n}$ 貌似卡不掉的样子。

同样是记录位置考虑本质就是知道了区间众数的出现次数 $k$，看是否存在另外的数出现次数 $> K$。我们直接 $\tt vector$ 中第 $i + K$ 个数是否在区间外即可。如果不是进行更新 $K$。

我们只要保证同样的数只遍历一次即可复杂度是 $O(B)$ 的。

所以 $B = \sqrt n$ 复杂度是 $O(n\sqrt n + q \sqrt n)$ 空间复杂度是 $O(n)$ 的。

##### **题意** : 区间逆序对，强制在线。

> [[Ynoi2019 模拟赛] Yuno loves sqrt technology I - 洛谷](https://www.luogu.com.cn/problem/P5046)

```cpp
提交 12.22k
通过 603
时间限制 750ms
内存限制 500.00MB
```

> 恐怖。

[直接跳转至正解](#jump1)

发现给定的数是**排列**。

考虑逆序对的限制是 $i < j, a_i > a_j$。

可以变成查询某个数在区间比当前数大的个数，或者说一个数在其之后有多少数比其要小。

考虑计算块内的答案设 $g(i, j)$ 表示块 $[i, j]$ 的答案。

枚举 $i$，先从 $g(i, j - 1)$ 进行转移之后考虑每个数的贡献。考虑每个数就是对于其后缀进行了 $+ 1$，树状数组维护。复杂度是 $O(\frac{n^2}{B} \log n)$ 的。

之后考虑查询的时候散块的贡献，先 $O(B \log B)$ 求出散块之间的逆序对，之后考虑整块对散块的贡献。

设 $pre(i, j), suf(i, j)$ 表示位置 $i$ 从其当前块（不计算）向后 $j$ 个块比其小 () 大的数的数量，另外那个是向前。查询的时候可以直接计算复杂度是 $O(B)$ 的，考虑如何预处理。

因为 $\tt pre, suf$ 本质一样，就说 $\tt suf$ 好了。

先枚举 $j$ 考虑同一个块一起处理，维护树状数组表示前缀和，复杂度是 $O(\frac{n^2}{B} \log n)$。

复杂度是：

$$
\begin{aligned}
\frac{n}{B} \log n &= B \log n\\\\
B &= \sqrt n
\end{aligned}
$$

复杂度是 $O(n \sqrt n \log n)$ 貌似过不去。

如果能省掉一个 $\log$ 可能会有点希望，考虑省掉求逆序对的 $O(B \log B)$。

考虑使用基数排序之后差分，双指针找到第一个不能产生贡献的位置，后缀和计算贡献。

复杂度是 $O(B)$ 的。

总的复杂度是 $O(n\sqrt{n \log n})$。

> 常数太大了，去世了。

<span id='jump1'></span>

想法还是类似的，考虑先计算块之间的贡献。考虑如何计算两块之间的逆序对的贡献。

> 使用归并排序，复杂度是 $O(B)$ 的。
> 
> 块之间的 $g(i, j) = g(i, j-1) + g(i + 1, j) - g(i + 1, j - 1) + V$。

总的复杂度是 $\frac{n^2}{B}$ 的。

之后处理 $f(x, i)$ 表示在前 $i$ 个块中 $\le x$ 的数的数量，计算的时候枚举 $i$ 每个 $p_i$ 使用差分产生贡献即可。

散块我们可以预处理出排序序列，之后归并，复杂度是 $O(B)$ 的。

所以总复杂度是 $O(n \sqrt n)$ 的。

> 归并的常数比较大。

考虑我们能否优化两个块之间的信息合并的常数，可以考虑计算 $g$ 时候预处理每个数在第 $i$ 个块的贡献，之后前缀和一下。

设 $f(i, j)$ 表示值 $i$ 与块 $j$ 中的贡献，之后前缀和。

那么考虑两个块的贡献的时候考虑已知 $i$ 通过差分就可以算得贡献。

对于散块和整块部分的贡献考虑对于每个 $i$ 在 $j$ 也做一遍前缀和，可以不用归并。

但是算散块之间的贡献还是得归并，常数较小。

---

一点小总结：

- 对于没有修改的，前缀和总是比单点来得有用。
  
  - 就算是有修改的也是同理。

- 查询的时候，尽量合并整块之间的信息的答案，然后单独计算散块和整块，散块和散块之间的贡献。

- 如果需要用到序列有序，可以预处理每块之间的顺序，散块直接取出来就行了。

- 如果碰到维护比较奇怪的求和问题，看区间信息能否合并，能否用别的方式表示。

- 如果碰到奇怪的最值问题，看这个最值是否有单调性，是否能套用凸包，观察数据范围。有点时候修改的数，值域可能是关键。

---

- #### $\color{blue}\Delta$ **块状数组**

某些分块可以看成 $3$ 层，每层是 $\sqrt n$ 叉树。

块状数组查询和修改复杂度一优一劣，用来处理修改和查询次数不同的情况。

- $O(1)$ 单点修改，$O(\sqrt n)$ 维护前缀和。

维护每块的和。

- $O(\sqrt n)$ 单点修改，$O(1)$ 区间查询。

维护每块的和，块之间的前缀和。

当值域大小为 $O(n)$ 时，可以用类似权值线段树的形式维护 `权值 n​ 叉树`。这个技巧被称为**值域分块**。

- $O(1)$ 插入一个数，$O(\sqrt n)$ 求区间第 $K$ 小。

维护块内有多少个数，直接暴力跳即可。

- $O(\sqrt n)$ 插入一个数，$O(1)$ 求区间第 $K$ 小。

考虑对于每个 $K$ 存储答案，对于每个 $K$ 维护其在哪个值域块内，会有 $\sqrt n$ 个段。

还要维护每个块前面数的个数，修改为 $O(\sqrt n)$。 

修改的时候每个段的端点会移动，复杂度是 $O(\sqrt n)$ 的。

对于每个块维护一个 $K$ 的范围，每次修改最多移动一个 $K$，使用插入排序或者链表是 $O(\sqrt n)$ 。

##### **题意** : 给定一个长度为 n 的序列和 m 个区间。

有两种操作：

- 把序列中的第 $x$ 个数改为 $y$
- 求第 $x$ 个区间到第 $y$ 个区间的和 (的和)

> [CC Chef and Churu](https://vjudge.net/problem/CodeChef-FNCS)

发现每个数如果暴力修改区间的话，复杂度会比较大。

考虑设 $f(i, j)$ 在第 $i$ 块中 $j$ 号点被覆盖了几次。

> 因为每个块是一个连续的区间，直接差分算贡献，复杂度 $O(\frac{n^2}{B})$。

可以在单点修改后 $O(\frac{n}{B})$ 算出每块区间的和，查询的时候 $O(B)$ 算出散区间的和，对于整块的区间可以使用前缀和 $O(1)$ 计算。

复杂度是 $O((n + q)\sqrt n)$ 的。

> 这个对应了 $O(\sqrt n)$ 修改， $O(1)$ 查询的块状数组。

#### **可持久化块状数组**

将上述三层 $\sqrt n$​ 叉树可持久化即可。可以搞出 `块状主席树` 之类的东西。

- #### $\color{blue}\Delta$ **块状链表**

不难发现块状链表就是一个链表，每个节点指向一个数组。 我们把原来长度为 $n$ 的数组分为 $\sqrt n$ 个节点，每个节点对应的数组大小为 $\sqrt n$  。 

需要支持分裂，插入，查询。

- 如果大小超过 $2 \times \sqrt n$ 就分裂成两个数组。

- ##### [带插入区间K小值](https://www.luogu.com.cn/problem/P4278)

> 字面意思。

> 之前 $\tt lxl$ 来的时候是用树套树做的。

考虑值域比较小，所以使用值域分块，如果知道值域的话可以在 $O(B)$ 的时间内查询第 $K$ 大。

考虑先对于序列分块，对于每个块下面也维护值域块的前缀和，每个值的前缀和。

空间是 $O(n\frac{n}{B})$ 的。

考虑查询的时候对于散块暴力构建，整块差分构建，合并即可，是 $O(B)$ 的。

考虑修改使用块状链表动态维护，当我们移动的时候只需要将前面一个块的信息转移即可。

复杂度是 $O(n\frac{n}{B})$ 的，我们取 $B = \sqrt n$ 可以得到最优复杂度 $O((n + q)\sqrt n)$。 

- #### $\color{blue}\Delta$ 分散层叠算法

[分散层叠算法(Fractional Cascading) - 洛谷](https://www.luogu.com.cn/problem/P6466)

- 给出 $k$ 个长度为 $n$ 的**有序数组**。

- 现在有 $q$ 个查询 : 给出数 $x$，分别求出每个数组中大于等于 $x$ 的最小的数(非严格后继)。

- 在线询问。

暴力怎么做，直接二分 $O(qk\log n)$。

考虑 $q$ 比较大没有什么操作空间，考虑通过预处理快速进行询问。

> $O(qk)$ 就已经比较紧了。

考虑一个很妙的算法，就是将所有的序列进行合并之后考虑二分第一个序列的某个位置 $x$。

对于每个序列 $y$ 的第 $i$ 个位置我们存储器对应到原第 $y$ 个序列的位置，和对应 $y + 1$ 个序列的位置。

可以发现我们如果知道了第一个序列的位置其他位置就对应找出来了。

但是空间复杂度实在是太大了，我们考虑只记录偶数的位置这样每个序列每个数的贡献就是 $1 + \frac{1}{2} \cdots = 2$。

所以时空复杂度都是 $O(n)$ 的。

但是我们最后还要左右横扫一下，这个复杂度证明证明呢？

考虑对于序列 $i$ 我们提取了序列 $i + 1$ 一半的元素，不妨考虑提取的位置是 $p, p + 2$。

如果在序列 $i$ 找到的位置是 $p$ 那么对于我们只需要搜索 $p + 1, p+ 2$ 两个位置了，本质上就是以 $\frac{1}{D}$ 的比例提取，在 $i + 1$ 区间就要查询 $D$ 次。我们是按照 $\frac{1}{2}$ 的比例进行提取的，所以这个 $D$ 可以看成常数，那么复杂度就是 $O(\log n + k)$。

> 注意建造的时候维护到原序列的位置。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

namespace Legendgod {
    namespace Read {
//        #define Fread
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
            for(; isdigit(c); c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
            x *= f;
        }
        template <typename T, typename...Args>
        void r1(T &x, Args&...arg) {
            r1(x), r1(arg...);
        }
        #undef getchar
    }

using namespace Read;

const int maxn = 1e4 + 5;
const int maxm = 1e2 + 5;
int n, m, a[maxm][maxn];

struct Node {
    int v, to, nxt;
}b[maxm][maxn << 1], tmp[maxn];
int ln[maxm], V1[maxn << 1];

void build() {
    int i, j;
    ln[m] = n;
    for(j = 1; j <= n; ++ j) b[m][j] = { a[m][j], j, 0 };
    for(i = m - 1; i >= 1; -- i) {
        ln[i] = 0;
        int tot(0), cur(1), cup(1);
        for(j = 2; j <= ln[i + 1]; j += 2) tmp[++ tot] = b[i + 1][j], tmp[tot].nxt = j;
        for(j = 1; j <= n; ++ j) {
            while(cur <= tot && tmp[cur].v <= a[i][j]) {
                tmp[cur].to = j;
                b[i][++ ln[i]] = tmp[cur], ++ cur;
            }
            while(cup <= ln[i + 1] && b[i + 1][cup].v <= a[i][j]) ++ cup;
            b[i][++ ln[i]] = { a[i][j], j, cup };
        }
        while(cur <= tot) tmp[cur].to = n + 1, b[i][++ ln[i]] = tmp[cur], ++ cur;
    }
    for(i = 1; i <= ln[1]; ++ i) V1[i] = b[1][i].v;
}

signed main() {
    int i, j, Q, D;
    r1(n, m, Q, D);
    for(i = 1; i <= m; ++ i) for(j = 1; j <= n; ++ j) r1(a[i][j]);
    build();
    int las(0);
    for(int _ = 1; _ <= Q; ++ _) {
        int x; r1(x), x ^= las, las = 0;
        int ps = lower_bound(V1 + 1, V1 + ln[1] + 1, x) - V1;
        for(i = 1; i <= m; ++ i) {
            while(ps <= ln[i] && b[i][ps].v < x) ++ ps;
            while(ps - 1 >= 1 && b[i][ps - 1].v >= x) -- ps;
            if(ps <= ln[i]) {
//                printf("%d : %d : %d\n", _, i, a[i][s]);
                las ^= a[i][b[i][ps].to], ps = b[i][ps].nxt;
            }
            else ps = ln[i + 1] + 1;
        }
        if(!(_ % D)) printf("%d\n", las);
    }
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }//
```

##### 分散叠层算法的普遍现象

现在有一个 $\tt DAG$ 每个点都有一个有序序列 $L_u$ ，每个点的出度入度在常数 $D$ 之内，我们想对任意路径查询非严格后继（也就是之前的问题）。

考虑对于节点 $u$ 建立一个序列 $M_u$ 除了其本身元素以外均匀选择其后继数组的元素，比如每个后继是 $\frac{1}{D}$。

同时记录其对应在 $L_u$ 的位置，和在每个 $M_v$ 中的结果。

对于一次查询现在路径起点进行二分查找，对于一个 $v$ 进行递归。

设查询的路径长度为 $k$ 则单次查询的时间复杂度为 $O(\log n + k)$。

##### 题意：区间加，区间 $rank$。

1. 分块

将 $B$ 个数分成一块，区间加打标记暴力重构，维护区间有序，$O(B)$。 查询的时候散块归并，整块还是直接二分，复杂度是 $O(B + \log B + \frac{n}{B}\log B)$。

取 $B = \sqrt{n\log n}$ 得到复杂度为 $O(n\sqrt{n \log n})$。

2. 还是分块，但是值域小。

考虑进行序列分块之后进行值域分块，对于每个整块进行维护值域块和值域的前缀和，复杂度是 $O(n \sqrt n)$ 的。

3. 分散层叠算法优化

优化分块一半使用 $\tt polylog$ 的结构，我们选择线段树。

建立线段树表示每个块表示为叶子节点，我们对于线段树儿子节点合并的时候使用该算法，设以 $\frac{1}{D}$ 的比例进行提取，考虑修改到块的时候需要进行重构，每次重构的复杂度是 $O(B\sum_i (\frac{2}{D})^i)$。$D > 2$ 的时候就没有 $\tt log$ 了。

考虑查询对于根进行二分然后子节点的所有信息都得到了，我们根据分散层叠算法扫描区间的全部子树，复杂度为 $O(\log n + \frac{n}{B} +B)$。

扫描的时候是带有常数 $D$ 的，所以我们取 $D = 3$ 就行了。

取 $B = \sqrt n$ 总的复杂度是 $O(n \log n + n \sqrt n)$。 

##### 题意：区间加区间最大值小于某数的子区间个数

> [[Ynoi2019] 魔法少女网站 - 洛谷](https://www.luogu.com.cn/problem/P6578)
> 
> 第 $10$ 分块的严格加强版。

先想个第 $10$ 分块，我们可以按照 $x$ 升序排序，那么我们就可以考虑对于 $\le x$ 点为 $1$，对于修改 $0 \to 1$ 就去世了，考虑将修改拿出来暴力解决。

也就是加入简单，删除麻烦，考虑询问分治即可。

> 对于每个值建立位置链表。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

namespace Legendgod {
	namespace Read {
		#define Fread
		#ifdef Fread
		const int Siz = (1 << 23) + 5;
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
			for(; isdigit(c); c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
			x *= f;
		}
		template <typename T, typename...Args>
		void r1(T &x, Args&...arg) {
			r1(x), r1(arg...);
		}
		#undef getchar
	}

using namespace Read;
typedef long long int64;
//bool bg;
const int maxn = 3e5 + 5;
const int maxm = 2e3 + 5;
int n, m, B, Bx, bl[maxn], L[maxm], R[maxm], a[maxn];
int64 clc[maxn];
int head[maxn], cnt(1);
struct Edge {
    int to, next;
}edg[maxn << 1];

int br[maxn], fa[maxn];
bool seg[maxn], usd[maxn];
int64 ans[maxn], sum[maxm];
struct Bk {
    int id, t[4], fl;
    int64 ans;
    void clear() { t[0] = t[1] = t[2] = t[3] = 0; }
    int& operator [] (const int& x) { return t[x]; }
    const int& operator [] (const int& x) const { return t[x]; }
}st[maxn];
int ed(0);

int ct1(0), ct2(0);
struct Node {
    int tm, x, l, r, id;
    int operator < (const Node& z) const { return x < z.x; }
}q2[maxn];
struct Node1 {
    int tm, x, y;
}q1[maxn];

inline void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

inline void Modify(const int& pos, const int& opt) {
//    printf("Cg = %d\n", pos);
    fa[pos] = pos, seg[pos] = 1;
    const int lst = pos - 1, nxt = pos + 1, tp = fa[lst];
    const int fl = (pos != L[bl[pos]] && seg[lst]), fr = (pos != R[bl[pos]] && seg[nxt]);
    Bk tmp; tmp.id = pos;
    if(!fl && !fr) tmp.ans = 1, tmp.fl = 0;
    else {
        tmp.fl = 1;
        int a = fa[lst], b = fa[nxt];
        if(fl && fr) {
            tmp.ans = 1ll * (nxt - a) * (b - lst);
            tmp[0] = a, tmp[1] = fa[a], fa[a] = b;
            tmp[2] = b, tmp[3] = fa[b], fa[b] = a;
        }
        if(fl && !fr) {
            tmp.ans = nxt - a;
            tmp[0] = pos, tmp[1] = fa[pos], fa[pos] = a;
            tmp[2] = a, tmp[3] = fa[a], fa[a] = pos;
        }
        if(!fl && fr) {
            tmp.ans = b - lst;
            tmp[0] = pos, tmp[1] = fa[pos], fa[pos] = b;
            tmp[2] = b, tmp[3] = fa[b], fa[b] = pos;
        }
    }
    if(opt) st[++ ed] = tmp;
    sum[bl[pos]] += tmp.ans;
}

inline void Back() {
    while(ed > 0) {
        const auto& tp = st[ed --];
        sum[bl[tp.id]] -= tp.ans;
        seg[tp.id] = 0;
        if(tp.fl) {
            fa[tp[0]] = tp[1], fa[tp[2]] = tp[3];
        }
    }
}

inline int64 Query(int sl,int sr) {
    int i;
    int64 ans(0);
    if(bl[sl] == bl[sr]) {
        int ct(0);
        for(int i = sl; i <= sr; ++ i)
            if(seg[i]) ++ ct;
            else ans += clc[ct], ct = 0;
        return ans + clc[ct];
    }
    int c1(0), c2(0);
    for(i = sl; i <= R[bl[sl]]; ++ i)
        if(seg[i]) ++ c1;
        else ans += clc[c1], c1 = 0;
    for(i = sr; i >= L[bl[sr]]; -- i)
        if(seg[i]) ++ c2;
        else ans += clc[c2], c2 = 0;
    int tp(c1);
    for(i = bl[sl] + 1; i <= bl[sr] - 1; ++ i)
        if(fa[L[i]] == R[i]) tp += R[i] - L[i] + 1;
        else {
            if(seg[L[i]]) ans -= clc[fa[L[i]] - L[i] + 1], tp += fa[L[i]] - L[i] + 1;
            ans += clc[tp] + sum[i], tp = 0;
            if(seg[R[i]]) ans -= clc[R[i] - fa[R[i]] + 1], tp += R[i] - fa[R[i]] + 1;
        }
    return ans + clc[tp + c2];
}

inline void Solve() {
    int i, j;
    cnt = 1; memset(head, 0, 4 * (n + 2)), memset(sum, 0, 8 * (n + 2)), memset(fa, 0, sizeof(fa));
    memset(seg, 0, sizeof(seg)) ;
    for(i = 1; i <= ct1; ++ i) br[q1[i].x] = 1;
    for(i = 1; i <= n; ++ i) if(!br[i]) add(a[i], i);
    sort(q2 + 1, q2 + ct2 + 1);
    int ps = 1;
    for(i = 1; i <= ct2; ++ i) {
//        printf("i = %d\n", i);
        while(ps <= q2[i].x) {
            for(j = head[ps];j;j = edg[j].next)
                Modify(edg[j].to, 0);
            ++ ps;
        }
        for(j = ct1; j >= 1; -- j) if(q1[j].tm < q2[i].tm && !usd[q1[j].x]) {
            usd[q1[j].x] = 1;
            if(q1[j].y <= q2[i].x) Modify(q1[j].x, 1);
        }
        for(j = 1; j <= ct1; ++ j) if(!usd[q1[j].x]) {
            usd[q1[j].x] = 1;
            if(a[q1[j].x] <= q2[i].x) Modify(q1[j].x, 1);
        }
//        printf("%d : \n", q2[i].tm);
        ans[q2[i].id] = Query(q2[i].l, q2[i].r);
        Back();
//        printf("i = %d\n", i);
        for(j = 1; j <= ct1; ++ j) usd[q1[j].x] = 0;
    }
    for(i = 1; i <= ct1; ++ i) br[q1[i].x] = 0;
}

//bool edd;

signed main() {
//    printf("%.3lf\n", fabs(&edd - &bg) / 1024.0 / 1024.0);
	int i, j;
    r1(n, m), B = 850 + 1;
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= n; ++ i) bl[i] = (i - 1) / B + 1;
    Bx = bl[n];
    for(i = 1; i <= Bx; ++ i) L[i] = R[i - 1] + 1, R[i] = i * B;
    R[bl[n]] = n;
    for(i = 1; i <= n; ++ i) clc[i] = 1ll * i * (i + 1) / 2;
    const int qm = sqrt(m * 13);
    for(i = 1; i <= m; i = j + 1) {
        j = min(i + qm, m), ct1 = ct2 = 0;
        for(int k = i; k <= j; ++ k) {
            int opt, l, r, x, y;
            r1(opt);
            if(opt == 1) r1(x, y), q1[++ ct1] = { k, x, y };
            else r1(l, r, x), ++ ct2, q2[ct2] = { k, x, l, r, ct2 };
        }
        Solve();
        for(int k = 1; k <= ct2; ++ k) printf("%lld\n", ans[k]);
        for(int k = 1; k <= ct1; ++ k) a[q1[k].x] = q1[k].y;
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```
