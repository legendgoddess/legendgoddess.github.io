---
title: 图论回忆1
date: 2022-4-15 9:08:00
tags:
  - 分块
categories:
  - 算法
sticky: 1
---
> 据说图论我会的东西相对少一点，我们先写图论。

## 二分图概念和判定

**定义**：对于无向图 $G = (V, E)$ 存在将 $V$ 划分成两个不相交子集 $A, B$ 满足集合内部没有连边，则 $G$ 为二分图。

**定理**：图 $G = (V, E)$ 是二分图，当且仅当 $G$ 中没有奇环。

**推论**：二分图 $G = (V, E)$ 的任意子图为二分图。

**推论**：二分图 $G = (V, E)$ 是二分图，当且仅当其任意连通子图是二分图。

> [封锁阳光大学 - 洛谷](https://www.luogu.com.cn/problem/P1330)
>
> 给定一张图，求是否存在点集 $V$ 满足任意一条边只有一个顶点在 $V$ 中。

本质上就是分成两个集合，使得集合内部没有边，就是二分图。

直接染色之后取点数小的即可。

> [[NOIP2010 提高组] 关押罪犯 - 洛谷](https://www.luogu.com.cn/problem/P1525)
>
> 给定一张无向带权图，将图分成两个集合，使得集合内部边权的最大值最小。

考虑权值大的肯定尽量是两个集合之间的边，考虑二分答案，之后找到不合法的边钦定一定要分为两个集合，设二分的答案为 $X$ 或者说建立图 $G = (V, E), E = \{ w > X | (u, v, w) \}$，是一个二分图。

> [[HNOI2019]校园旅行 - 洛谷](https://www.luogu.com.cn/problem/P5292)
>
> 给定以一张无向图，每个点有点权 $0, 1$，对于 $q$ 个询问 $s, t$ 求是否存在 $s \to t$ 的一条路径使得路径上的点权是回文串。
>
> $O(V^2 + q)$。

能否直接预处理出来两个点之间的答案，考虑一个点到另外一个点点数的奇偶性是固定的。

对于标记全部是相同的大小 $\ge 2$ 的连通分量，只需要考虑其增加这一段的奇偶性，如果该连通分量是二分图那么改变的奇偶性是固定的。

- 二分图只要保留一个生成树就行了。

> 因为树也是二分图。

- 如果不是二分图，直接考虑生成树之后在根节点加一个自环就行了。

对于连接不同颜色的边我们肯定是一个二分图，所以保留生成树就行了，所以边数是 $O(n)$ 的。

考虑暴力枚举转移的边，之后进行 $\tt bfs/SPFA$ 转移即可。

## 二分图最大匹配

对于给定 $G = (V, E)$，求出最大的边集 $A \subseteq E$ 使得没有两条有共同端点的边。

**增广路定义：** 对于一个没匹配端点节点 $p$ 出发，交题经过在 $A$ 中和不在 $A$ 中的边，到达一个没匹配节点。那么这条路径被称为一条增广路。

**匈牙利算法：**

如果 $P$ 是二分图最大匹配，当且仅当图中不存在增广路。首先必要性显然，考虑充分性。

1. 不会出现可以直接加入匹配的边。
2. 如果修改边的匹配那么一定存在一条路径上面的边进行翻转，但是已经没有增广路了，所以不存在。

综上充分性成立。

**实现过程：**

维护 $1 \sim i - 1$ 点的最大匹配，每次寻找增广路。

如果设左边点的标号为 $1$，右边为 $0$ 那么最大匹配就是最大字典序的。

> [[NOI2009] 变换序列 - 洛谷](https://www.luogu.com.cn/problem/P1963)
>
> 给定数列 $A_{1 \sim n}$ 求字典序最小的排列 $P$ 使得 $|P_i - i| = A_i$。

考虑对于每个 $i$ 其确定了连接的两条或者一条边，直接进行连边。

那么左边是 $P_i$ 右边是 $i$，找到最大匹配就是答案。

考虑从后向前跑就是字典序最小的了。

### 二分图最小点覆盖

**定理**：二分图最小点覆盖大小等于最大匹配大小。

**推论**：任何一组点覆盖大小 $\ge$ 任何一组匹配大小。

二分图最大匹配的线性规划形式：

$$
\begin{aligned}
&\max\sum_{i = 1} ^ m x_i \\\\
&s.t.
\begin{cases}
\sum_{i = 1} ^ n w(j, i) x_i \le 1\\\\
x_i \ge 0
\end{cases}
\end{aligned}
$$

其中 $x_i$ 表示该边是否选择，$w(j, i)$ 表示点 $i$ 是否被边 $j$ 选择。

其对偶问题是：

$$
\begin{aligned}
&\min\sum_{j = 1} ^ m y_j \\\\
&s.t.
\begin{cases}
\sum_{j = 1} ^ m w(j, i) y_j \ge 1\\\\
y_j \ge 0
\end{cases}
\end{aligned}
$$

其中 $y_j$ 表示点 $j$ 是否被选择。

> $\tt ix35:$ 我们可以证明：上面问题中的代价矩阵是一个**全幺模矩阵**，因此其最优解等于整数规划解，即 $x_i \in \{0, 1\}$，这说明了我们用线性规划描述二分图匹配的合理性。

### 二分图最大独立集

**定理**：最大独立集和最小点覆盖之和等于点数。

**推论**：二分图最大独立集大小与最大匹配大小之和等于点数。

> [骑士共存问题 - 洛谷](https://www.luogu.com.cn/problem/P3355)
>
> 在 $n \times m$ 的棋盘上最多放置多少不会相互攻击的骑士。

考虑让 $2|i + j$ 成为左部点，剩下成为右部点。对于点的限制两两连边，本质上就是求最大独立集。

### 二分图最小边覆盖

**定义**：用最少的边覆盖所有点。

如果不存在孤立点，答案就等于最大独立集大小。

### 有向无环图最小路径覆盖

求 $\tt DAG$ 的最小路径覆盖。

构造拆点二分图，有如下定理：

**定理**：最小路径覆盖 $=|V| - $ 拆点二分图最大匹配。

> 根据 $\tt Dilworth$ 定理，考虑路径是一个偏序集，那么最小链覆盖等于最大反链长度。

> **链定义：** $\forall (x, y) \in V, x \to y \ \cup\  y \to x$。

> **反链定义：** $\forall(x, y) \in V, x \not\to y \ \cap \ y \not \to x$。

> 所以反链本质就是一个独立集，这样的独立集 $=|V| - $ 最大匹配。

> 如何求 $\tt DAG$ 的最大匹配呢？直接拆点二分图。

考虑拆点二分图 $G_0$。

- 对于路径 $v_1 \to v_2 \to \cdots \to v_n$ 可以表示成 $v_i \to v_{i + 1}$ 因为路径不相交，所以肯定是可以表示成匹配的形式。
- $G_0$ 的一组匹配同上可以构造成 $G$，对于没有边经过的点都视为被空路经过。

> [[CTSC2008]祭祀 - 洛谷](https://www.luogu.com.cn/problem/P4298)
>
> 貌似是我多年前以为找不到的原题。
>
> 给定的 $\tt DAG, G = (V, E)$，求最大的 $A \subseteq V$ 使得 $\forall x, y \in A,$ 不存在 $x$ 到 $y$ 的路径。

本质上就是最大独立集，先一手传递闭包。最大反链就是最小链覆盖。

### 二分图最大权匹配

**定义**：给定二分图 $G = (V, E)$，每条边有权值 $w_i$ 我们需要找到一组匹配使得边权最大。

> 不一定是最大匹配。

还是使用线性规划，$x_i$ 表示 边 $i$ 是否选择。

$$
\begin{aligned}
&\max\sum_{i = 1} ^ m w_i x_i \\\\
&s.t.
\begin{cases}
\sum_{i = 1} ^ n w(j, i) x_i \le 1\\\\
x_i \ge 0
\end{cases}
\end{aligned}
$$

依旧对偶：

$$
\begin{aligned}
&\min\sum_{j = 1} ^ m y_j \\\\
&s.t.
\begin{cases}
\sum_{j = 1} ^ m w(j, i) y_j \ge w_i\\\\
y_j \ge 0
\end{cases}
\end{aligned}
$$

我们设 $y_i$ 表示点 $i$ 的顶标，那么本质上就是如下问题：

**最小顶标和问题**：给定二分图，给每个点一个顶标，使得每条边两端点的顶标和不小于边权，且所有点顶标和最小。

> $w_i \ge 0$，如果 $ \exists w_i < 0$ 那么就不能使用这种做法，不满足线性规划的条件。
>
> 如果对于每个点加入一个极大权值，需要满足最大匹配是完美匹配。

先讲述如何将最大权匹配转化成最大权完美匹配、

- 如果要求 $w_i \ge 0$ 的**最大权匹配**，那么我们只需要将所有不存在的边视作边权为 $0$ 的边，如果左右两部点数不相等就补位相等。
- 如果要求**存在** $w_i < 0$ 的最大权**完美**匹配，就将不存在的边权值设为 $- \infty$。
- 如果要求**存在** $w_i < 0$ 的最大权匹配，那么当然是把所有负权边删掉，变成非负的情况。

我们将左边点顶标称为 $A_i$ 右部点 $j$ 的顶标称为 $B_j$，我们要求 $A_i + B_j \ge d(i, j)$，其中 $d(i, j)$ 为 $i, j$ 之间的边权。

**相等子图：**所有满足 $A_i + B_j = d(i, j)$ 的边构成的子图。

**命题**：相等子图中若存在完美匹配，那么这组完美匹配是原图的一个最大权完美匹配。

证明：对于边权和显然是 $\sum A_i + \sum B_j$，那么对于原图中任意的一组完美匹配有 $d(i, j) \le A_i + B_j$ 所以取到最大边权和。

如果相等子图中没有完美匹配，考虑调整法。

假设左侧有点 $x$ 在相等子图的最大匹配中是非匹配点，让 $x$ 尝试找增广路（虽然是没有的）考虑对于左边的顶标减少 $\Delta$ 右边增加 $\Delta$。

- 匹配边，两边要么都能访问到，要么都不能访问到，还是属于相等子图。
- 对于某个以访问到的左端点为一端的非匹配边，由于 $A_i$ 减小，有可能加入相等子图中。

我们取 $\Delta = \min(A_i + B_j - d(i, j))$ 即可，这样至少加了一条	边。

**优化**：$\tt Slack$ 优化的基于 $\tt Bfs$ 的 $\tt KM$ 算法。

- 每次尝试增广的时候模拟匈牙利算法求增广路的过程，对于右边的每个点 $y$，记录 $\tt Slack_y$ 表示这一轮已经访问的左侧点 $x$ 中，$\min(A_x+B_y-d(x, y))$。
- 当我们访问一个左部点的时候，先用其更新右部点的 $\tt Slack$ 值，接下来取出右部 $\tt Slack$ 最小的点，将其值设为 $\Delta$，之后修改左右部分的所有点，并且更新 $\tt Slack$ 数组。

> 也就是 $\tt Slack$ 数组减去 $\Delta$。

- 将下一个访问的左部点设为取出的右部点的匹配点，如果没有被匹配那就找到了一条增广路。
- 重复上述过程，得到最大权完美匹配。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

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

const int maxn = 500 + 5;
typedef long long int64;
const int64 inf = 1e15;
int n, m, mt[maxn], pre[maxn];
int64 ex[maxn], ey[maxn], sl[maxn], vc[maxn][maxn];
bool vis[maxn];

void bfs(int u) {
    int x, y(0), yy(0), i; int64 tmp;
    memset(pre, 0, sizeof(pre));
    for(i = 1; i <= n; ++ i) sl[i] = inf;
    mt[y] = u;
    while(1) {
        x = mt[y], tmp = inf, vis[y] = 1;
        for(i = 1; i <= n; ++ i) if(!vis[i]) {
            if(sl[i] > ex[x] + ey[i] - vc[x][i]) {
                sl[i] = ex[x] + ey[i] - vc[x][i];
                pre[i] = y;
            }
            if(sl[i] < tmp) tmp = sl[i], yy = i;
        }
        for(i = 0; i <= n; ++ i) {
            if(vis[i]) ex[mt[i]] -= tmp, ey[i] += tmp;
            else sl[i] -= tmp;
        }
        y = yy;
        if(mt[y] == -1) break;
    }
    while(y) mt[y] = mt[pre[y]], y = pre[y];
}

int64 KM() {
    memset(ex, 0, sizeof(ex));
    memset(ey, 0, sizeof(ey));
    memset(mt, -1, sizeof(mt));
    for(int i = 1; i <= n; ++ i) {
        memset(vis, 0, sizeof(vis));
        bfs(i);
    }
    int64 res(0);
    for(int i = 1; i <= n; ++ i) if(mt[i] != -1)
        res += vc[mt[i]][i];
    return res;
}

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) for(j = 1; j <= n; ++ j) vc[i][j] = - inf;
    for(i = 1; i <= m; ++ i) {
        int u, v; int64 w;
        r1(u, v, w), vc[u][v] = w;
    }
    printf("%lld\n", KM());
    for(i = 1; i <= n; ++ i) printf("%d%c", mt[i], " \n"[i == n]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```

> 例题 $7$：$\text{[UVA 1411] Ants}$
>
> 给定平面上 $n$ 个黑点和 $n$ 个白点，请选 $n$ 条线段，每条线段连接一个白点和黑点，每个点只作为一条线段的端点，且这些线段两两不交。
>
> 一定存在解。

考虑线段两两不交的限制，也就是考虑若 $AB \cap CD \neq \emptyset$，那么有 $AB+ CD > AD + BC$，因为一定存在解，所以考虑不相交的线段肯定是边权和最小的。

也就是求连接黑点和白点的长度和最小的 $n$ 条线段对应的匹配，直接执行 $\tt KM$ 算法。

### $\tt Hall$ 定理

对于二分图 $G = (V, E)$ 另 $N(v)$ 表示与 $v$ 相邻的点集，则关于最大匹配，我们有如下结论：

**定理**：设二分图的两部分为 $X, Y$ 且 $|X| \le |Y|$，则其存在一个大小为 $|X|$ 的匹配，当且仅当 $\forall S \subseteq X$，有 $|S| \le |\bigcup_{v \in S} N(v)|$。

> 就是说其能到达的点集 $V \subseteq Y, |S| \le |V|$。

证明：

必要性：若存在 $S$ 使得条件不成立，所有点的匹配点形成了一个大小不小于 $|S|$ 的点集，其中肯定存在一个子集的大小大于超集。

充分性：若条件成立但是不存在大小为 $|X|$ 的匹配，考虑从左部点开始尝试增广，因为匹配不成功所以递归终止点必然是**左部点**，考虑遍历到的所有左部点有 $|L|= |R| + 1$ 与条件矛盾。

**推论1：**若一个无向图的每个点度数都为 $k$，则成其为 $k$ 正则图，那么左右点数相等的 $k$ 正则二分图必有完美匹配 （$k \ge 1$）。

> 证明：反证法，设存在左部点集 $L$ 设其邻居并为 $R$ 有 $|L| > |R|$，那么 $R$ 中所有点的度数和不小于 $k \times |L|$。但是 $R$ 的大小 $< L$ 是不可能的。

**推论2：** 如果点带权，左部点 $i$ 需要匹配 $a_i$ 个右部点（可重复），而右部点 $i$ 可匹配 $b_i$ 个左部点（可重复），我们将定理中的 $|S|$ 和 $|\bigcup_{v \in S} N(v)|$ 改成其元素和即可。

### $\tt Hall$ 定理推广

**定理：**设二分图 $G$ 的两部分是 $X,Y$，则其最大匹配为 $|X| - \max(|S| - |\bigcup_{v \in S} N(v)|)$，其中 $S \in X$。

证明：

令 $f(S) = |S| - |\bigcup_{v \in S}N(v)|$。

首先最大匹配不会超过 $|X| - f(S)$，考虑 $f(S)$ 取到最大值的 $S$，其点的总匹配数不会超过 $|\bigcup_{v \in S} N(v)|$。

所以整张图的最大匹配数不会超过 $|X| - \max(f(S))$。

> 我们感性理解一下， 就是考虑一个集合其最大的不能匹配的点数是 $\max(f(S))$，就算剩下的点都能进行匹配，那么就是 $|X| - f(S)$。

考虑如果最大匹配小于这个数，从左部的非匹配点进行增广，对于所有递归过程构成了以左部点**非匹配点**为根，所有叶子为左部点的森林，设左部非匹配点有 $s$ 个，不妨设左右点数分别为 $l, r$，满足 $l = r + s$，因为 $s$ 是一个 $f(S)$，所以左部非匹配点不超过 $\max(f(S))$ 个。

> 例题 $8$：
>
> 给定左部 $n$ 个点，右部 $m$ 个点的二分图，左部点 $i$ 向右部 $[l_i, r_i]$ 内的所有点连边，求是否存在大小为 $n$ 的匹配。

> $\tt Hall$ 定理就是求匹配的存在性。

也就是判断条件：$\forall S \subseteq X, |S| \le \bigcup_{v \in S} N(v)$ 是否成立。

首先考虑一手按照区间进行排序，那么我们最终选择的肯定是连续的排序之后连续的左部点。

> 如果区间重合很好处理，如果不重合呢？

如果区间有重合可以考虑式子能直接使用线段树查询，如果区间不重合，考虑先求出每个重合区间的答案。

发现如果每个子区间合法那么合并这个两个区间肯定合法。

> 对啦！！！

考虑当不存在大小为 $n$ 的匹配是，一定存在一个区间作为 $R$ 使得 $\tt Hall$ 定理的条件不成立，不妨设区间为 $[l, r]$ 要算出左部点满足 $[l_i, r_i] \subseteq [l, r]$ 的 $i$ 个数，减去 $r - l + 1$ 之后取 $\tt max$。

使用数据结构维护这一过程，升序扫描 $r$，对于每个 $l$ 维护当前 $[l, r]$ 对应的 $|L| - |R|$，当 $r$ 增大时如果有 $r_i = r$ 的左部点 $i$，则需要将 $l \le l_i$ 的所有 $l$ 对应的 $|L|-|R|$ 增大 $1$，然后每次询问全局最大值如果存在正值就不存在大小为 $n$ 的匹配。

> 首先升序枚举 $r$ 肯定是所有点都加入进去的，然后我们只需要考虑线段树维护重复的贡献，我们不需要考虑线段树的 $l$ 是否真的存在，因为对于真实存在的点 $l$ 取到的肯定是最值。
>
> 所以直接一起维护即可。

我们接下来继续分析这张图匹配的特性。

将所有左部点按照 $r_i$ 从小到大排序，考虑第一个左部点 $x$ 应该匹配哪个右部点。

根据 $\tt hall$ 定理，设左部点集为 $V_L$，对于 $A \subseteq V_L$ 且 $x \not \in A$，我们希望删除 $x$ 及其匹配点之后对 $\bigcup_{v \in S} N(v)$ 影响最小，也就是尽量使其不变。

也就是尽量使其不变，因为第一个点的限制我们有 $r_x \le r_i$，所以选择取 $l_x$，如果只有单个点对于 $A \cup x$ 来说是会更优的，不然是不变的。

我们贪心撇皮每次选择当前可以匹配点中最靠前的一个没有被匹配过的点，若每个点都被匹配，那么就找到了一个大小为 $n$ 的匹配。

这启发我们：**Hall 定理可以作为一些显式或隐式匹配问题的贪心策略依据。**

> 例题 $9$：
>
> 给定左部 $n$ 个点，右部 $m$ 个点的二分图，左部点 $i$ 向右部 $[1, l_i] \cup [r_i, m]$ 内的所有点进行连边，求最大匹配。

也就是求 $n - \max(|S| - \bigcup_{v \in S} N(v))$。

如果前缀是递增的，我们考虑选择 $i$ 那么前缀的部分肯定没有用了，对于后缀我们可以维护 $i - 1$ 的区间答案每次加入一个区间的时候使用区间加。

之后分类讨论如果点 $i$ 的后缀是最大的，那么就是查询 $r$ 小于当前值的贡献，不然考虑加入当前值求最优贡献。

复杂度是 $O((n + m)\log m)$  的。

枚举左部点集 $L$ 其右部点邻居集 $R$ 必然是一段前缀并后缀。

考虑枚举 $R = [1, l] \cup [r, n]$，同上题可以得到关于 $L$ 中的点的 $l_i, r_i$ 的限制，使用数据结构升序扫描 $l$，对于每个 $r$ 维护 $|L| - |R|$，当 $l$ 增加是若遇到 $l_i = l$ 的 $i$，则对于 $r \le r_i$ 的 $r$，其 $|L| - |R|$ 将增大 $1$。

> 例题 $10$：
>
> 给定左部 $n$ 个点，右部 $m$ 个点的二分图，左部有点权 $a_i, b_i$，右部点有点权 $c_j, d_j$，且 $(i, j)$ 之间有边当且仅当 $a_i \ge d_j$ 或者 $c_j \ge b_i$ 求最大匹配。

首先右部点按照 $d$ 从小到大排序，左部点按照 $a$ 从小到大排序，可以发现左部点 $i$ 连接的肯定是一段前缀，之后考虑右部点。

考虑从 $0$ 开始枚举 $d$ 使用线段树维护 $c = x$ 的答案。

当增加 $d$ 使得位置 $i$ 被加入之后，对于同时满足 $c, d$ 限制的贡献被计算了两遍，所以需要减去贡献。

复杂度是 $O((n + m) \log n)$ 。

### 网络最大流

给定有向图 $G = (V, E)$，每条边有非负容量 $c_i$，同时给定两个不同点 $s, t \in V$ 分别表示源点，汇点，则称这是一张网络图。

令 $c(u, v)$ 表示当 $u, v$ 之间有有边是等于这些边的容量和，否则等于 $0$。

定义一个合法流函数 $f:V\times V \to R$ 是满足如下性质的函数：

- $f(u, v) \le c(u, v)$。
- $f(u, v) = -f(v, u)$。
- 令 $fl_i = \sum_{j = 1} ^ n f(i, j)$ 有 $fl_s \ge 0, fl_t \le 0$ 对于其他点都有 $fl_i = 0$。

> 1. 流量上限。
> 2. 流量方向。
> 3. 除了 $s, t$ 外每个点都不存储流，所有流入的流量都会流出。

最大流问题就是对于给定网络图找出了一个合法的流函数，使得 $fl_s$ （称为流量）最大。

**基本性质：**

- $fl_s = - fl_t$。
- 若不存在 $i \to j$ 和 $j \to i$ 的边，则必有 $f(i, j) = f(j, i) = 0$，这是因为 $c(i, j) = c(j, i) = 0$。

根据性质 $2$ 我们只需要记录边的点对之间的流，或者说只需要记录每条边上运载的流的大小。

$\tt Dinic$ 算法：

先确定一个任意的初始流函数，比如所有 $f(i, j) = 0$，随后不断尝试扩大最大流。

**残量网络**：设当前已有一个流函数 $f$，则取 $c'(i, j) = c(i, j) - f(i, j)$ 为个条边容量得到的新的网络图。

**增广路**：我们任取一条 $s \to t$ 的路径 $v_1 \to \cdots \to v_n(v_1 = 1, v_n = t)$ 使得 $c'(v_i, v_{i + 1}) > 0$。

最大流算法都用到一个**重要性质：当前函数是最大流，当且仅当参量网络不存在增广路**。

暴力思路就是暴力找增广路，因为流量有限所以算法肯定会在有限次增广后结束。

$\tt Dinic$ 进行了优化，首先对于参量网络进行分层，然后增广的时候只保留出发点的层数比终点层数恰好小 $1$ 的边，显然是一个 $\tt DAG$。

**优势：**多路增广，增广使用 $\tt dfs$，分层使用 $\tt bfs$。

> 当前弧优化可以保证每条边用完之后就不会再遍历到。

最大流可以解决二分图最大匹配问题：建立源汇点 $s, t$ 从 $s$ 向所有左部点连接容量为 $1$ 的边，汇点对右部点连接容量为 $1$ 的边。

> 比匈牙利算法要快。

> [[SDOI2013]费用流](https://www.luogu.com.cn/problem/P3305)
>
> 给定网络图，$\tt Alice$ 需要给出一个最大流对应的流函数，随后$\tt Bob$ 可以给每条边赋予非负费用 $c_i$，要求所有边费用总和为 $P$，使得 $\tt Alice$ 的最大流中所有边的流量乘费用求和最大，$\tt Alice$ 希望这个最大值最小，求这个最大值的最小值。

考虑 $\tt B$ 的策略是啥，就是选择流量最大的边，让其费用为 $P$。

可以发现这样肯定是最优的。

> 反证法：如果有两条边流量分别为 $a, b, a > b$，然后费用为 $c, P - c$，贡献是 $ac + bP - bc$。和 $aP$ 相比会小很多，所以不合法。

那么考虑如何通过一些方法让最大边权最小，直接二分答案之后求最大流即可。

> 二分答案我们每条边边权对其取 $\tt min$。

> [ [USACO07OPEN]Dining G](https://www.luogu.com.cn/problem/P2891)
>
> 有 $n$ 头牛，$f$ 种食物和 $d$ 种饮料，每种食物和饮料只有一份，每头牛有一些喜欢的食物和饮料，求最多有多少头牛可以得到自己喜欢的食物和饮料。

考虑左边放食物右边放饮料，每头牛拆点连边。

最大流问题还可以用于解决一些其他模型。

#### 最小割

对于有向图 $G$，每条边有非负代价 $c_i$，求删除代价最小的边集使得 $s$ 到 $t$ 不连通。

**定理：**最小割等于将每条边的代价转化成其容量后 $s$ 至 $t$ 的最大流。

证明：

首先我们只需要考虑 $s$ 能到达或者能到达 $t$ 的点。

我们定义一组割为将 $V$ 拆成两个不相交的子集 $L, R$ 其中 $s \in L, t \in R$，我们删除所有 $L, R$ 之间的边。显然这是一组割，将这样的割称为 $C(L, R)$。

1. 对于 $G$ 中任意的合法流函数，其中 $fl_s$ 不超过 $G$ 的最小割。

考虑 $L$ 中所有点的 $fl$ 之和，首先是等于 $fl_s$ 同时也是所有 $L \to R$ 的边的容量和。

2. 最大流对应的参量网络中无增广路。
3. 若有一个流对应了无增广路的参量网络，则他也对应了一个不超过其 $fl_s$ 的割。

考虑将 $L$ 设为残量网络中 $s$ 可以到达的点集，剩下点放到 $R$ 中，显然中间的边都是满流的，所以 $fl_s$ 不小于这组割。

所以这个流的流量恰好等于割的大小。

由此我们证明下面三个命题等价：

- $G$ 中存在一组割 $C(L, R)$ 等于 $G$ 中合法流函数 $f$ 的流量。
- $G$ 中的流函数 $f$ 是最大流。
- $G$ 中关于 $f$ 的残量网络是无增广路的。

同时我们还可以得到刚才的一个结论：无增广路的残量网络必然对应最大流。

> 首先必要性显然，根据我们之前证明的最大流等于最小割，而最小割的残量网络是无增广路的。

我们刚刚解决的是有源汇的最小割，对于无源汇的最小割有算法 $\tt Stoer-Wagner$ 可以在 $O(VE + V^2 \log V)$ 解决该问题。

#### 无源汇的最小割

考虑暴力就是枚举所有的源点和汇点，进行最小割。

显然对于图中 $a, b$ 两点其要么在同一个连通块要么处于不同连通块。

- $a, b$ 在同一连通块。

显然如果 $a$ 与 $j$ 是不连通的，可以推出 $b$ 与 $j$ 是不连通的，我们可以将 $a, b$ 看成同一个点进行处理。

- $a, b$ 在不同连通块、

构造过程开始于一个集合 $A$，初始是 $A = \emptyset$。我们将所有点按照一个权值 $w(i)$ 从大到小加入。

令 $w(i) = \sum_{j \in A} d(j, i)$ 其中 $d(j, i)$ 表示 $i, j$ 之间的边权。

设 $ord(i)$ 表示点 $i$ 加入集合的顺序，我们可以得到对于任意点 $s$，$s$ 到 $t$ 的最小割即为 $w(t)$。

证明：

若点 $v$ 满足在割去 $C$ 的图中，存在一个点 $u$，在 $v$ 之前加入 $A$，且 $u$ 和 $v$ 是不在一个连通块的，则称 $v$ 是 $\tt active$ 的。

考虑我们取最后一个节点 $t$ 的权值 $w(t)$ 作为最小割。因为割掉这些边之后图是不连通的，而且我们之前贪心得到的答案是最小的。换句话说 $w(t)$ 就是 $t$ 到之前所有节点的最小割。

对于第一个 $\tt active$ 节点 $v$，结论成立：因为 $v$ 前节点都不是 $\tt active$ 节点，所以都处于一个连通块内，所以 $v$ 与它们都不处在一连通块内，所以想要割掉 $v$ 需要断开它和之前所有点，花费是 $w(v)$。

对于 $v$ 之后的第一个 $\tt active$ 节点 $u$，有 $w(u)$ 是从 $v$ 前节点和 $v$ 到 $u$ 之间节点与 $u$ 的边构成的，对于 $v$ 前面的点有 $w(u)$ 对于这部分的贡献不超过 $w(v)$。同样道理对于 $v$ 到 $u$ 之间的节点与 $u$ 边权相加一定要出现在 $C$ 中，所以结论成立。

根据归纳法结论成立。

[Stoer - Wagner 算法](https://www.luogu.com.cn/problem/P5632)

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

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

const int maxn = 600 + 5;
int n, m;
bool vis[maxn], us[maxn];
int d[maxn][maxn], S, T, w[maxn], ord[maxn];

int Solve1(int x) {
    memset(us, 0, sizeof(us));
    memset(w, 0, sizeof(w));
    w[0] = -1;
    for(int i = 1; i <= n - x + 1; ++ i) {
        int ps(0);
        for(int j = 1; j <= n; ++ j) if(!vis[j] && !us[j] && w[j] > w[ps]) ps = j;
        us[ps] = 1, ord[i] = ps;
        for(int j = 1; j <= n; ++ j) if(!vis[j] && !us[j])
            w[j] += d[j][ps];
    }
    S = ord[n - x], T = ord[n - x + 1];
    return w[T];
}

int Solve() {
    int i, j, res(1e9);
    memset(vis, 0, sizeof(vis));
    for(i = 1; i < n; ++ i) {
        res = min(res, Solve1(i));
        vis[T] = 1;
        for(j = 1; j <= n; ++ j) {
            d[S][j] += d[T][j];
            d[j][S] += d[j][T];
        }
    }
    return res;
}

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int a, b, w;
        r1(a, b, w);
        d[a][b] += w, d[b][a] += w;
    }
    printf("%d\n", Solve());
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```

> [[USACO 5.4] Telecowmunication](https://www.luogu.com.cn/problem/P1345)
>
> 给定无向图 $G$ 和点 $s, t$，求最少删除多少个非 $s, t$ 的点可以使得 $s$ 到 $t$ 不连通。

拆点最小割。

> 如果一条边不能被割的话，就将代价设为 $+\infty$。

#### 最大权闭合子图问题

给定有向图 $G$，每个点有可负点权 $a_i$，选出点集的子集 $A$，满足若 $u \in A,(u, v) \in E$ 则 $v \in A$。求 $\sum_{u \in A} a_u$ 的最大值。

考虑求的是最大值，那么如果一个点连边了一个正值点，那么这两个点肯定都入选，所以我们对于一个点需要考虑的就是能够到达的负权点。

选择一个点权为负的点相当于吧答案减掉它的权值，可以理解成我们需要舍弃这个点的权值。

因此我们将问题转化成了类似二分图的问题：将点权非负的点向它能到达的所有点权为负的点连边，再从 $s$ 向点权非负的点分别连边，点前为负的点向 $t$ 连边。

> $S$ 连接的是边权和 $T$ 连接的是边权的绝对值。
>
> 答案就是正权点 $-$ 最小割。

就是我们考虑构建的时候权值为负的点是需要减去的，权值为正的点不选也是需要减去的。考虑我们选择一个正权点，肯定会带来其周围的负权点的贡献，显然我们不会考虑直接选择一个负权点。

所以我们只需要考虑选择正权点的贡献，使用最小割求出选择最小的权值减去。

也就是如果该限制被选择了那么其限制的点应该全部被选择。

#### 最大流方案

可以直接算出每条边的流量。

#### 最小割方案与可行边必经边

对于最小割的方案，我们再跑完最大流之后求出 $s$ 能到达的所有点集合 $L$ 和其他点构成的集合 $R$，对于边 $(u, v)$ 如果 $u \in L$ 而且 $v \in R$ 那么边 $(u, v)$ 就在最小割中。

**可行边：**存在一种最小割使得这条边被割断。

**必经边：**每个最小割这条边都被割断。

> 暴力：
>
> - 可行边：考虑删除这条边，图的最小割减少了 $w$。
> - 必经边：将这条边代价改为 $+ \infty$ 图的最小割变大。
>
> 事实上只需要判断必经边就行了。

**命题：**一条边 $(u, v, w)$ 是最小割可行边，当且仅当这条边满流，并且 $G'$ 上不存在 $u \to v$ 的路径。

证明：

满流显然，如果存在 $u \to s \to t \to v$ 的路径那么删除掉之后最小割不会减少 $w$，不然最小割会恰好减少 $w$。

**命题：**一条边 $(u, v, w)$ 是最小割必经边，当且仅当这条边满流，并且 $G'$ 上存在 $s \to u$ 和 $v \to t$ 的路径。

证明：

满流显然，如果不存在 $s \to u$ 的路径，那么就算加大该边的容量最大流也不会变大，$v \to t$ 同理。

**定理：**将 $G'$ 进行 $scc$ 缩点，则边 $(u, v,w)$ 是**可行边**当且仅当满流且 $u, v$ 在不同的强连通分量。是**必经边**当且仅当 $s, u$ 在同一强连通分量，$v, t$ 在同一强连通分量。

所以将 $G'$ 缩点得到的 $\tt DAG$ 连接不同的 $scc$ 的满流边是可行边，从 $s$ 所在 $scc$ 连接 $t$ 所在 $scc$ 的满流边是必经边，而从 $s$ 所在 $scc$ 连出去的边构成了最小割的一种方案。

#### 二分图最大匹配方案与可行点和必经点

因为是拆点所以我们就说成可行边和必经边了。

**半增广路定义**：一条路径依次通过非匹配边和匹配边，首尾分别是匹配边和非匹配边，并且终点是非匹配点。

**增广环定义：**一个环依次经过非匹配边和匹配边。

显然对于半增广路和增广环的边取反可以得到一个新的最大匹配。

**定理：**对于任意两组最大匹配 $P_1, P_2$ 总存在一种方法使得进行若干次取反半增广路或取反增广环的操作，使 $P_1$ 变成 $P_2$。

证明：显然不会出现两条单链不重复而且有交的情况，如果出现的话这个就不是最大匹配。那么对于单链是可以通过翻转解决的。

考虑链连接一个环的情况，也是同理。

对于这样两个匹配对于奇链肯定是相同的方案数的，所以只有偶链和环也就是半增广路和环。

**命题：**边 $(u, v)$ 是二分图最大匹配的**可行边**，当且仅当 $(u, v)$ 属于当前最大匹配或存在一条经过 $(u, v)$ 的半增广路或增广环。

> 显然。

**命题：**边 $(u, v)$ 是二分图最大匹配的必经边，当且仅当 $(u, v)$ 属于当前最大匹配且不存在一条经过 $(u, v)$ 的半增广路或增广环。

> 显然。

考虑将**半增广路**也转换成**增广环**，建立网络图：

- 建立源点 $s$ 和汇点 $t$，从 $s$ 向左部非匹配点连边，左部匹配点向 $s$ 连边。从 $t$ 向右匹配点连边，右部非匹配点向 $t$ 连边。
- 对于原二分图中的边，如果属于当前最大匹配，就从右部点连向左部点，否则就从左部点连向右部点。

本质上就是保留原来的增广环，对于半增广路我们需要用一种方法变成环，不妨考虑钦定原图上的匹配边是由右部点向左部点连接的，那么对于一条半增广路，如果其起点在右部点。其终点肯定也是在右部点，且起点是匹配点，终点是非匹配点。我们让右部的非匹配点向 $t$ 连边，$t$ 向匹配点连边。就转化成环了。

> 另外一边同理。

显然对于不经过 $s, t$ 的环就是增广环。

对于新的有向图 $G'$，包含一条边的增广路或增广环等价于边的两点在同一 $\tt scc$ 中。

**定理：**边 $(u, v)$ 是二分图最大匹配的可行边，当且仅当它属于当前匹配或 $u, v$ 属于 $G'$ 中同一 $\tt scc$。而 $(u, v)$ 是二分图最大匹配的必经边，当且仅当它属于当前匹配，而且 $(u, v)$ 不属于 $G'$ 中同一 $\tt scc$。

$\tt Bonus:$ 二分图匹配最大可行点？

任何度数不为 $0$ 的点都是可行点，其出边能找到长度至少为 $2$ 的半增广路。

$\tt Bonus:$ 二分图最大匹配必经点？

不存在这个点为端点的半增广路，也就是其和 $s$ 不属于同一强连通分量。

### 最小费用最大流

给定一张网络图，每段点除容量 $c(u, v)$ 外还有价值函数 $w(u, v)$ 求一个就函数 $f(u, v)$ 使得源点流量最大的情况下，$\sum w(u, v ) \times f(u, v)$ 最小，这个问题被称为**最小费用最大流**，简称**费用流**。

请注意，我们下面讨论的是**价值函数不构成负圈**的网络图上的费用流问题，而有负圈的情形将在介绍上下界网络流后说明。

**残量网络的定义**：残量网络每条反向边价值为正向边的相反数。

> 反悔的定义。

令 $F_i$ 表示流大小为 $i$ 的最小费用，我们考虑如下算法：

- $F_0 = 0$。
- 在第 $i$ 轮，以价值为边权，找出当前残量网络上 $s \to t$ 的最短路，将其附上 $1$ 的流，并让 $F_{i + 1} = F_i + l$ 其中 $l$ 表示为路径上所有边的价值和。

> 证明：如果存在更小的费用 $F_n'$，我们已知 $F_{n - 1}$ 增广了最优的增广路，但是有更小的价值肯定是存在负环，显然对于这个负环我们肯定是可以提前增广的。那么 $F_{n - 1}$ 就不是最优的，与假设矛盾。

**命题**：在 $F_i$ 的残量网络找到最短路，若路上所有边的最小容量大于 $1$，则 $F_{i + 1}$ 的残量网络上该路径也是最短路。

证明：

假设不是，那么 $F_{i + 1}$ 的残量网络最短路肯定经过了一条这条路径上边的反向边。

设其中第一条这样的反向边为 $u \to v$，价值为 $w$，那么我们对于 $F_{i + 1}$ 最短路的 $s \to u$ 价值和为 $w_1$ 经过了 $-w$ 到达了 $v$，对于 $F_i$ 中的最短路 $s \to v$ 价值为 $w_2$ 我们有 $w_1 - w < w_2$ 也就是 $w_1 < w_2 + w$ 那么这个就不是最短路，与假设矛盾。

所以我们可以直接通过最短路求解，通过 $\tt johnson$ 可以直接使用 $\tt Dijkstra$。

> 复杂度是 $O(n(n+m)\log n)$ 当然如果使用配对堆的话可以做到 $O(n^2\log n + nm)$。

上述的费用流算法成为 $\tt SSP(Successive\ Shortest \ Path)$ 算法，该算法不仅可以求出最大流时的最小，而且可以求出流量依次为 $0, 1,\cdots,f$ 的最小费用，但是前提是**无负环**。

当要求最大费用的时候需要将边权取反来求最小费用，而存在负环的最小费用流可以利用上下界网络流解决。

费用流可以解决二分图最大权最大匹配。

> 但是比 $\tt KM$ 要慢，其可以求解大小为 $i$ 的匹配的最大权值。

> [P2604 [ZJOI2010]网络扩容](https://www.luogu.com.cn/problem/P2604) 第二问。
>
> 给定一个网络图，每条边有费用 $w(u, v)$，表示让 $u \to v$ 的容量加 $1$ 需要付出 $w(u, v)$ 的代价，使求得 $1 \to n$ 最大流增大 $k$ 的最小代价。

我们先算出最大流的费用，之后建立新的源点向 $1$ 连边容量为 $f+ k$，之后每条边改为 $(u, v, +\infty, w)$。

#### 模拟费用流

**定义**：有一类费用流问题由于图的特殊性，可以使用一类特殊贪心来解决。

费用流这种每次选取一条最短路进行增广的想法让我们联想到贪心，但保证费用流正确性的是其反悔机制——反向边用于退掉之前更劣的流，因此我们考虑在贪心中也贯彻这一点。

这个东西我们之后再讲，先学后面的。

#### 上下界网络流

现在将网络图的定义做一些调整，除了流量上线（容量）$c(u, v)$ 外，每对点还存在流量下限 $b(u, v)$，此时的流函数 $f(u, v)$ 还应该满足 $b(u, v) \le f(u, v) \le c(u, v)$，将这样的问题称为**上下界网络流**。

> - 有无源汇。
> - 可行流，最大流，最小流，费用流。

#### 无源汇上下界可行流

最基础的问题 ：给定一张上下界网络图，求一种合法的流量函数，使得每个点都满足流量守恒。

考虑将问题转化成没有下界的网络流。

首先强制每条边流到下界，也就是 $f'(u, v) = b(u, v)$ 令 $c'(u, v) = c(u, v) - b(u, v)$，这个时候一个点的流量差为 $f(u) = \sum b(u, i) - \sum b(i, u)$，然而这可能是不满足流量守恒的。

对于一个 $f(u) > 0$ 的点 $u$ 其出流量太多了，所以需要大小为 $f(u)$ 的入流量来抵消他，就让 $u$ 向外界连一条流量恰好为 $f(u)$ 的边。

同理对于 $f(u) < 0$ 的点，同样要让外界向 $u$ 连接一条流量为 $-f(u)$ 的边。

建立超级源点和超级汇点，剩下的边的容量设为 $c’(u, v) = c(u, v) -b(u, v)$。

根据 $\sum f(u) = 0$ 可以知道 $S$ 的出容量等于 $T$ 的如容量，只有这些边全部满流才满足流量守恒，所以如果最大流恰好等于 $F$ 说明可行流存在，且残量网络的容量就是 $c(u, v) - f(u, v)$ 的值。不然可行流就不存在。

#### 有源汇上下界可行流

考虑转化成上一个问题，有源汇本质上就是可以满足 $f(s) > 0, f(t) < 0$ 显然对于合法的流我们有 $f(s) = -f(t)$。

那么我们让 $t$ 向 $s$ 连接一条流量为 $+\infty$ 即可。

> 本质上就是源汇之间有流交换，可以保证流量平衡。

#### 有源汇上下界最大流

先保证可行流。

记录 $t \to s$ 边的流量，这个就是当前 $s \to t$ 的流量。

因为已经满流了，所以我们只需要在参量网络上做一次普通的 $s \to t$ 的最大流即可。

> 实现的时候可以不删掉 $t \to s$ 的边。

或者二分答案，将 $t \to s$ 边的下界设为 $m$ 看是否存在可行流。

#### 有源汇上下界最小流

保证可行流，记录 $s \to t$ 的流量。

考虑我们原来求最大流就是不断从 $s$ 到 $t$ 找增广路，选择只是反过来不断退增广路。

所以本质上就是用当前流量减去参量网络上 $t \to s$ 的最大流。

#### 无源汇上下界最小费用可行流

同样建图，新增的边价值为 $0$。

将初始费用设为 $\sum b(u, v) \times w(u, v)$，然后跑 $s \to t$ 的费用流。

> 流的可行性同理。

#### 有源汇上下界最小费用流

可行流时直接连边 $t \to s$ 变成无源汇情况。

如果是最小费用最大流本质一样，跑完 $S \to T$ 的最小费用最大流之后再跑 $s \to t$ 的。

如果是最小流就是从 $t \to s$ 跑流，退流。

> 费用改成最大费用就直接取反就行了。

#### 有负环的费用流

对于一条价值为负数的边 $(u, v, c, w)$ 我们假设这条边流满，即答案费用加上 $w \times c$，然后建反边 $(v, u, c, -w)$ 用于退流，此时就全是正权边了！

但是这有一个问题，现在的 $u, v$ 可能不满足流量守恒，但是前面所说的上下界的基本思路和这里是一样的，还是建立超级源点汇点。

如果是无源汇就是 $S \to T$ 的正权费用流，如果有源汇那么就跑完 $S \to T$ 之后跑 $s \to t$。

> [P4843 清理雪道](https://www.luogu.com.cn/problem/P4843)
>
> 给定 $DAG$ 求最少需要几条路径能覆盖所有**边**至少一次。

$\tt Dilworth$ 定理是不行了，考虑转换成上下界网络流。

考虑边覆盖那么流至少为 $1$，之后让 $s$ 向所有点连边 $[0, +\infty]$，所有点向 $t$ 连边 $[0, +\infty]$。

**重点：**将覆盖转化成流量下界。

### 网络流扩展与建模举例

常见想法与转化：

- 多源问题：

    如果一道题中有多个可行的原点$s_1, \cdots, s_a$ 和多个可行的汇点 $t_1, \cdots, t_b$，那么我们可以建立超级源汇 $S, T$，从 $S$ 向 $s_i$ 连容量无穷的边，$t_i$ 向 $T$ 连容量无穷的边， 转化成单源汇问题。

- 点转边：

    如果有`一个点只能有不超过 f 的流经过` 之类关于点的限制，可以拆点连入边到入点，出边到出点，入点和出点之间连接容量为 $f$ 的边。

- 边转点：

    有些问题中（不一定是网络流），需要将边转化成点，可以考虑为每个边新建一个点，向两端连边。

- 出入流量差限制：

    如果网络图中对于某些点$i$ 有其出流量减去如流量恰好等于 $f_i$，就按照上下界网络流一样的建图方法。对于 $f_i > 0$ 的点让 $S$ 向它连边，不然就让它向 $T$ 连边，容量为 $- f_i$，随后要加上 $S$ 或 $T$ 相关边的满流的限制。

- 双选问题：

    如果一道题中某个元素有两种（或多种）可能的取值，可以先假设取其中一个代表，比如说最小值，再用流标记调整成较大的。

#### 最大流建模

> [P2766 最长不下降子序列问题](https://www.luogu.com.cn/problem/P2766) 第二问
>
> 给出一个长度为 $n$ 的序列 $a_{1 \cdots n}$，求最多能选出几个不相交的最长不下降子序列。

考虑分层图。

设 $f(i)$ 表示最后一个元素为 $a_i$ 的最长不下降子序列长度。

考虑怎样的子序列 $b_1,\cdots,b_m$ 是合法的：

- 满足 $f(b_{i + 1}) = f(b_i) + 1$ 且 $f(b_1) = 1, m$ 恰好是最长不下降子序列长度。

不妨设最长不下降子序列长度为 $s$，将所有点按照 $f(i)$分成 $s$ 类形成分层图，对于 $f(j) = f(i) + 1$ 让 $i \to j$ 进行连边，之后拆点保证每个点的流量限制。

之后将 $S$ 向第一层点连边，最后一层点向 $T$ 连边。

**重点：**分层图和方案数的关系。

> 所以我们可以对于给定图计算起点到终点的 点 / 边 不相交最短路的最大数量，考虑最短路 $\tt DAG$。
>
> 边的话就将边的容量设置为 $1$，连边还是类似的。

> [P3163 [CQOI2014]危桥](https://www.luogu.com.cn/problem/P3163)
>
> 给定无向图，两个人分别要走 $2 \times a_n$ 次 $a_1 \to a_2$ 的任意路径和 $2 \times b_n$ 次 $b_1 \to b_2$ 的任意路径，某些边可以任意多次经过，某些边只能经过两次，求是否存在合法方案。

首先很直接的想法就是两个源点汇点，这样边的限制就满足了，但是如果存在一条路径中途改变的情况。

如果两条路径有交那么我们是可以直接使其合法的。

因为第一次跑是不一定合法的，所以我们考虑能不能通过另外的方式来验证合法性，也就是判断路径的可行性。

显然我们的最大流肯定是 $2\times (a_n + b_n)$。

考虑直接建立流可能会出现 $a_1 \to b_2$ 的情况，如果说有另外的一条路径满足 $b_2 \to a_2$，那么肯定是可行的。

当然对于 $b_1 \to a_2$ 也是同理。

考虑进行跑两次流，第一次是源点为 $\{a_1,b_1\},$ 汇点为 $\{a_2, b_2\}$ ，第二次是源汇分别是 $\{a_1, b_2\}, \{a_2, b_1\}$ 。

如果两次流都是满的说明是可行的。

证明：

首先如果第一次流没有流满，直接说明连路径的个数都不满足了显然不合法。

考虑如果第二次没有流满的情况同上。

我们考虑对于满足条件是否一定能构造出来方案。

假设第一次流真实情况下 $a_1 \to a_2$ 偏少，那么我们需要 $a_1 \to b_2 \to a_2$ 来弥补。

因为第二次和第一次本质上图是没有什么变化的，所以 $b_2 \to a_2$ 的方案数恰好是缺少的部分。

对于第一次因为流总量是不变的 $a_1 \to b_2$ 肯定相对多了一些，多的部分也就是 $a_1 \to a_2$ 少的部分。

所以通过拼接是可以得出合法方案的。

#### 最小割建模

> [P2598 [ZJOI2009]狼和羊的故事](https://www.luogu.com.cn/problem/P2598)
>
> 给定 $n \times m$ 网格中每个给子有狼，有羊，或者都没有的状态，求周长总和最小的一些边线，使得一头狼不经过这些变现的情况下走到任意一样处。

羊设为源点，狼设为汇点，一条边线割断的代价为 $1$，多源汇最小割。

除此之外，考虑最小割建图时会利用其二元性质：一个点与 $s, t$ 其中一个连通。

##### 最小割模型 $1$ (双选模型)：

> 有 $n$ 个物品，需要将它们分成 $A, B$ 两个集合，第 $i$ 个唯品分入 $A$ 集合的代价是 $a_i$，分入 $B$ 集合的代价为 $b_i$，对于 $i, j$ 两个物品如果它们所属集合不同则需要额外付出 $c(i, j)$ 的代价求最小总代价。

> 经典题。

首先考虑其在 $S$ 不妨设为 $A$ 集合，如果在 $T$ 就是 $B$ 集合，单向边。

之后我们只需要考虑所属集合不同的代价，也就是两点直接连边容量为 $c(i, j)$，如果 $c(i, j) = c(j, i)$ 直接连双向边。

点数为 $O(n)$ 边数为 $O(n +m)$ 其中 $m$ 是非零联合代价数量。

**变形：**

- 如果一个集合最初属于其中一个集合，换到另一个集合需要代价 $d_i$，那么分入原来集合的代价就是 $0$，到另一个集合的代价就是 $d_i$，不建对应的边即可。
- 只有 $i$ 属于 $A$ 集合且 $j$ 属于 $B$ 集合才需要付出 $c(i, j)$ 的代价：双向边改成单向边。

> [P2057 [SHOI2007]善意的投票 / [JLOI2010]冠军调查](https://www.luogu.com.cn/problem/P2057)
>
> 有 $n$ 个小朋友，每个小朋友有一个 $0/1$ 值，要取反一个小朋友的值需要 $1$ 的代价，有 $m$ 对小朋友是好朋友，如果他们值不一样则需要付出 $1$ 代价，求最小总代价。

值 $0/1$ 本质上就是看其属于哪个集合，钦定 $S$ 表示集合 $0$。

套用上述做法即可。

> [P4076 [SDOI2016]墙上的句子](https://www.luogu.com.cn/problem/P4076)
>
> 题面太长了。

> 题面很强。

注意 `翻转过来也是一个单词` 说明单词肯定是成对出现的。

首先对于行和列建点，钦定和 $S$ 连边的是正串。

> 钦定正串的字典序比反串要小。

那么首先回文串不管怎么样都是 $1$ 的贡献，所以特判掉。

对于剩下确定正反的串让其直接跟 $S$ 或者 $T$ 连边 $+\infty$。

对于正串和反串之间的贡献考虑连边 $2$ 表示同时出现。

之后考虑行列需要满足对于同样的看的方式串是有限制的。

考虑两个串的联合贡献就是一些单词的集合，我们让每行每列对于其包含的单词的起点，终点连边 $+\infty$。

因为题目保证了对于确定读法是符合条件的，我们可以先算出是全正串还是全反串。

对于全正串的部分我们将 $S$ 向其连接 $+\infty$ 否则就是其向 $T$ 连接 $+\infty$。然后对于每个行列连接单词的起点和终点。

对于没有确定的行和列我们本质上不需要连 $S, T$ 的边。

> 因为这个东西可以看情况而定的，所以本质上就是 $S, T$ 都连接上一个权值为 $x$ 的边，然后 $x$ 是不产生贡献的所以 $x = 0$。

注意上述模型的使用限制：

- 每个元素的决策是二元的（也就是只有 $S, T$ 连通两个选择）
- 联合贡献只能是**两个元素**之间的。
- 联合贡献对答案是负影响（也就是这样的贡献越少越好）。

如果第二个或者第三个条件不满足则需要用到最大权闭合子图的建图方法，通常情况下点数会增大成联合权值的个数。

> 可以看下面[最大权闭合子图问题](#jump1)

##### 最小割模型 $2$（串联模型）：

> 有 $n$ 个 $[1, m]$ 中的数 $a_1, \cdots, a_n$（不是给定的），将 $a_i$ 赋值成 $j$ 需要付出 $v(i, j)$ 的代价，有 $q$ 个限制，每个限制形如 $a_x - a_y < z$ （可改变不等号方向）。

将每个点 $a_x$ 拆成 $m + 1$ 个点，第 $i$ 个点称为 $a_x(i)$，将这些点串联，其中第 $i$ 到第 $i + 1$ 个点的容量是 $v(x, i)$，并从 $s$ 向第一个点连边，最后一个点向 $t$ 连边，容量都是无穷。那么这条链上的最小割就是恰好割掉中间的一条边。

考虑限制 $a_x - a_y < z$ 我们从 $a_x(i)$向 $a_y(i - z)$ 连容量为 $+\infty$ 的边，表示如果 $a_x$ 选择了 $\ge i$ 的数，$a_y$ 就不能选择 $\le i - z$ 的数。

但是可能存在一个点割掉了若干条边满足限制，所以先对约束求一遍**最短路**，对每一对点列建边。

或者可以考虑建边 $a_x(i + 1) \to a_x(i)$ 容量无穷，表示每个位置都需要满足之前限制的条件。

图中的点数为 $O(nm)$ 边数为 $O((n + q)m)$。

简单变形：

- 将 $a_i$ 赋值为 $j$ 可以获得收益：先加上最大收益，再讲每个数的收益与最大收益作差作为代价。最后就是相减了。
- 不等号方向改变：$a_y - a_x < - z$。

> [P3227 [HNOI2013]切糕](https://www.luogu.com.cn/problem/P3227)
>
> 给定所有 $v(x, y,z)(x \in [1, P], y \in [1, Q], z \in [1, R])$，求一组 $f(x, y)$ 使得相邻的 $(|x_1 - x_2| + |y_1 - y_2| = 1)$ 二元组的 $f$ 值相差不超过 $D$，且所有 $v(x, y, f(x, y))$ 之和最大。

不妨考虑重新标号我们有 $- D \le v_x - v_y \le D$ 就是上述的限制了。

之后是最小割杂题。

> [P5039 [SHOI2010]最小生成树](https://www.luogu.com.cn/problem/P5039)
>
> 给定无向图，每次操作可以将一条边权增大 $1$，问使得给定的边一定在此图最小生成树中至少需要先执行几次操作。

一定存在说明考虑不大于这条边的其他边中两端点是不连通的。

本质上就是考虑删除若干条边让两端点不连通，直接最小割即可。

> [P5458 [BJOI2016]水晶](https://www.luogu.com.cn/problem/P5458)
>
> 题面较为复杂。

首先坐标系有 $x + y + z = 0$ 我们考虑如果让 $x' + y' = 0$ 而且有 $-z' = x' + y'$ 那么我们有 $x' + y' + z' = 0$ 同样也是一个合法的坐标系，可以发现这个是满射。

考虑对于坐标系进行 $\mod 3$ 染色，我们只需要考虑 $\mod 3 = 0$ 的点进行考虑，显然如果选了三个点有 $\mod 3 = 0, 1, 2$ 那么肯定是不合法的。

让能量源作为中间点，两种颜色作为两边点，相邻颜色进行连边，拆点最小割。

> [P3756 [CQOI2017]老C的方块](https://www.luogu.com.cn/problem/P3756)
>
> 题面复杂。

发现这样奇怪的限制是很难处理的，考虑网络流题可以先考虑染色，对于长度为 $4$ 的不同限制我们可以考虑染 $4$ 种颜色。

发现都可以表示成 `黄蓝绿红` 的路径，那么我们考虑进行连边。

- $S \to $ 黄，边权为移除黄色的代价。
- 黄 $\to$ 红，边权为 $\tt inf$。
- 红 $\to$ 黑，边权为移除红色或黑色的最小值。
- 黑 $\to$ 绿，边权为 $\tt inf$。
- 绿 $\to T$，边权为移除绿色的代价。

复杂度是 $O(C + n^2n)$ 的，但是 $\tt dinic$ 复杂度与图的深度有关所以因为图的深度很小复杂度也是对的。

#### 最大权闭合子图建模

<div id = "jump1"><div>

> [P4174 [NOI2006] 最大获利](https://www.luogu.com.cn/problem/P4174)
>
> 有 $n$ 座基站，第 $i$ 座建造代价为 $p$，还有 $m$ 个用户群，每个用户群只要基站 $a_i, b_i$ 都建了就会带来 $c_i$ 的收益，问最大净利润。

我们只需要考虑用户群是否生效，如果该用户群生效就意味着 $(u, v)$ 一定都被选择。

考虑使用最小割解决最大权闭合子图，对于用户群和基站建点，分别是左边，右边的点。考虑左边连接的是贡献，右边连接的是代价即可。

> [[NOI2009]植物大战僵尸](https://darkbzoj.tk/problem/1565)
>
> 给定一个 $n \times m$ 的矩阵，僵尸只能从右向左沿着一条直线走。
>
> 击杀某个植物会获得收益 $p_i$，$p_i$ 不一定是正数。
>
> 每个植物有攻击集合，僵尸不能走到攻击集合上，当然你可以吃掉某个植物再走。
>
> 求僵尸的最大收益。

如果吃掉这个某个植物首先要吃掉该行之后的所有植物，还要吃掉该路径上攻击范围的所有植物才能获得贡献。

也就是意味着如果选择了该植物就必须选择其他的某些植物。

考虑如果 $x$ 守护了 $y$ 就建立边 $y \to x$ 如果 $x$ 在 $y$ 的右边也同理。

但是对于大小 $> 1$ 中的强联通分量是不能选择的，我们去掉这些点，之后再跑最大权二分图匹配即可。

##### 集合联合贡献：

> 从 $n$ 个元素中选出一些，每个元素选择有代价，有 $m$ 个元素集合，如果某个集合中的元素全都选了就会有一个对应的收益求最大净收益。

左部点为集合，右部点为元素。

> [P3410 拍照](https://www.luogu.com.cn/problem/P3410)
>
> 模板。

有时一些贡献会比较抽象，且贡献之间可能还互相影响，需要更多边来描述：

> [P3749 [六省联考 2017] 寿司餐厅](https://www.luogu.com.cn/problem/P3749)
>
> 题面复杂。

考虑如果选择了 $[i, j]$ 那么就意味着一定需要选择 $d(l, r), i \le l \le r \le j$。

为了缩减边数我们考虑二维前缀和一样连边，只需要限制 $d(i - 1, j), d(i, j - 1)$。

对于贡献 $cx$ 可以考虑加在 $d(i, i)$ 对于 $mx^2$ 的考虑让所有 $x$ 连接他，之后连接到 $T$ 贡献是固定的。

还是最大权闭合子图。

---

注意最小割一定是比最大权闭合子图更强的模型：例如如果在最大权闭合子图的基础上将“选一个点而不选其后继”的代价从 $+ \infty$（做不到）变为常数 $x$，只需要在构图上改变中间边的边权即可，可以用最小割模型解释。

或者说可以先尝试使用最大权闭合子图建立模型，之后通过最小割来调整。

#### 费用流与上下界网络流建模

#### 区间选择模型：

给定 $[1, m]$ 中的 $n$ 个区间 $[l_i, r_i]$，每个区间选择一次的代价为 $w_i$，最多可以选 $c_i$ 次，要求使得任意点 $j$ 被覆盖次数在 $[a_j, b_j]$ 之间，求最小 / 最大代价。

将 $1$ 到 $m + 1$ 连成一条链，尝试用 $i \to i + 1$ 边的流量刻画 $i$ 被覆盖的次数。

建立 $l_i$ 到 $r_i + 1$ 的一条边称为区间边，如果区间边上流过一条流，就表示选择一次这个区间。

源流向 $1$，$m + 1$ 流向汇，如果我们确定总流量 $X$ 那么没有流经 $i \to i + 1$ 这条边就是选择了区间，那么如果 $i \to i+ 1$ 的流量为 $X - f_i$ 就表示 $i$ 被覆盖了 $f_i$ 次。

那么我们有一种比较显然的做法，就是考虑对于每个 $i \to i + 1$ 的边流量设置为 $[X - b_i, X - a_i]$，之后将区间费用设为区间代价，容量设为可选次数，对于 $i \to i + 1$ 的边费用为 $0$。

使用最小 / 最大费用上下界最大流即为答案。

这里 $X$ 可以选择一个充分大的数 $> \max b_i$。

> [P3358 最长 $k$ 可重区间集问题](https://www.luogu.com.cn/problem/P3358)
>
> 从给定的 $m$ 个开区间中选择尽量多的区间，使得每个位置被不超过 $k$ 个区间覆盖，且区间长度和最大。

$w = 1, c = 1, a = 0, b = k$。

由于所有 $b_j$ 相等，所以可以没有下界，能直接普通费用流。

> [P6967 [NEERC2016]Delight for a Cat](https://www.luogu.com.cn/problem/P6967)
>
> 在 $n$ 个小时中的每个小时，猫可以选择吃饭或睡觉，每个小时吃饭或睡觉有一个愉悦毒，但要求任意连续 $k$ 小时中至少有 $m_e$ 小时吃饭，至少 $m_s$ 小时睡觉，问最大总愉悦度。

可以考虑每个小时都睡觉之后进行决策哪些时间吃饭。

所以连续 $k$ 小时吃饭的时间在 $[m_e, k - m_s]$ 之间。

考虑将第 $i$ 小时吃饭的贡献，其可以对于区间右端点在 $[i, i + k - 1]$ 的区间造成贡献，那么对于每个点就有一个覆盖限制。

$c = s_i, w =0, a = m_e, b = k - m_s$，同上题可以跑费用流。

> [P3980 [NOI2008] 志愿者招募](https://www.luogu.com.cn/problem/P3980)
>
> 有 $m$ 个可选操作，每个操作为将 $s_i$ 到 $t_i$ 中每个位置 $+ 1$，代价为 $c_i$，每个操作可以做无限次，要求最后位置 $i$ 上的数不小于 $a_i$，求最小代价。

也就是对于位置 $i$ 的数要 $\ge a_i$。

模型中的 $w = c_i, c = +\infty, b = +\infty$ 让 $X = b$ 就没有下界了。

> 不要担心设 $\tt inf$。

#### 费用流的凸性

对于一条边，设其第 $i$ 次流过的代价是 $a_i$，当 $a_i$ 不降时才适合用费用流描述该问题。

> 每次都是最短路显然。

**这一点在模拟费用流时也可以利用，一般来说决策的权值满足凸性的问题可以化为费用流问题，否则一般不行。**

> [CF436E Cardboard Box](https://www.luogu.com.cn/problem/CF436E)
>
> 有 $n$ 个关卡， 对于第 $i$ 个关卡可以花费 $a_i$ 的代价获得 $1$ 颗星，也可以花费 $b_i$ 的代价获得 $2$ 颗星，求获得 $w$ 颗星的最小代价。
>
> $O(n \log n)$

返回贪心。。。

> [P4249 [WC2007]剪刀石头布](https://www.luogu.com.cn/problem/P4249)
>
> 给一张已经部分定向的竞赛图定向，使得三元环最多。

考虑三元环不好计算考虑计算非三元环，也就是对于每个非三元环肯定有一个点的出度为 $2$，这可以唯一确定一个非三元环。

所以非三元环的个数就是 $\sum_i \binom{deg_i}{2}$。

考虑我们现在就是在钦定边的顺序，对于边 $(i, j)$ 就是决策哪边度数 $+1$。

考虑 $\binom{a}{b}$ 是不好计算的，所以考虑差分。

不妨考虑点 $j$ 已经定向了 $d_i$ 的出度，我们考虑新增的出度 $x$ 对于 $x \to x + 1$ 费用为 $\binom{d_i + x}{2} - \binom{d_i + x - 1}{2}$。

每个点向 $T$ 点连接若干条边，这个表示反悔贪心，因为这个贡献是有凸性的所以是满足费用流的。

#### 图覆盖模型

给出一些条件，要求用一些链或圈覆盖图的所有点。

通常使用二分图模型来描述这类问题：

- $\tt DAG$ 不相交路径覆盖，差点建立二分图，如果右边 $x\to y$ 则从左部点 $x$ 向右部点 $y$ 连边，一个匹配就对应一个不相交路径覆盖，其中匹配边 $x \to y$ 表示 $x, y$ 是一条路径上的前驱后继关系。

为什么只能做 $\tt DAG$？因为若不是 $\tt DAG$ 这里就会成环，就不是不相交路径覆盖了。

- 最少路径的不相交路径覆盖：和最大匹配对应。

> 或者可以使用 $\tt dilworth$ 定理理解。

- 最大权不相交路径匹配：等价于最大权二分图匹配的权值。
- $\tt DAG$ 可相交路径覆盖：先传递闭包之后是等价的。
- 不相交圈覆盖：给定一张任意有向图，一样建立二分图，每一组完美匹配就对应一个圈覆盖，左部点 $i$ 的匹配点就是其圈上的后继。
- 最小 / 大权不相交圈覆盖：拆点二分图求最小 / 大权二分图匹配。

> [P6061 [加油武汉]疫情调查](https://www.luogu.com.cn/problem/P6061)
>
> 给定有向带权图，每次你可以选择一个非空回路，付出其权值和的代价以覆盖其中一个子集，也可以选择一个点 $u$ 付出 $a_u$ 的代价覆盖这个点，求覆盖所有点的最小总代价。

考虑走环中的边 $(i, j)$ 的最小代价是 $d(i, j)$ 直接使用 $\tt floyd$ 即可，直接使用 $\tt KM$ 即可。

> [P4542 [ZJOI2011]营救皮卡丘](https://www.luogu.com.cn/problem/P4542)
>
> 有 $n + 1$ 个点（$0$ 到 $n$ 编号）的有向带权图，$k$ 个人从 $0$ 出发，每个人每次只能走到编号不超过之前走到过的最大编号 $+ 1$ 的点，每个点要至少被一个人走过，问最小的所有人走过的边边权之和。

首先根据这个限制可以得到如果当前走过的最大的是位置 $i$ 说明 $0 \sim i$ 都已经被遍历过一次了。

可以考虑设 $d(i, j)$ 表示从 $i$ 经过不超过 $j$ 的点到达 $i$ 的最短路，这个东西本质就是传递闭包了。

那么每个点至少被一个人走过可以直接跑费用流。

#### 上下界网络流建图

当一道题限定了一个点或一条边的流量时，使用上下界网络流解决问题。

如果一个点不满足流量守恒，可以使用类似上下界网络流的处理手法，建立超级源汇解决问题。

> [P4553 80人环游世界](https://www.luogu.com.cn/problem/P4553)
>
> 用 $m$ 条路径覆盖一个 $\tt DAG$，第 $i$ 个点恰好经过 $v_i$ 次，每条边都有边权，求总边权最小。

每个点拆点限制流量，拆点二分图跑最小费用最大流。

> [CF708D Incorrect Flow](https://www.luogu.com.cn/problem/CF708D)
>
> 给定一张网络图和其流函数，不一定满足 $f(u, v) \le c(u, v)$ 和流量守恒，每次操作可以增大或减少一个 $f(u, v)$ 或 $c(u, v)$，求最小操作次数，使得流函数合法。

- $f(u, v) \le c(u, v)$

可以直接对于 $f$ 进行增大 $(u, v, c(u, v) - f(u, v), 1)$。

可以考虑退流 $(v, u, f(u, v), 1)$。

可以考虑满流之后增大 $(v, u, +\infty, 2)$。

- $f(u, v) > c(u, v)$

先需要计算修正代价，预先加上 $c(u, v) - f(u, v)$。

增广正向边 $(u, v, +\infty, 2)$。

退流不小于 $c(u, v)$，反向边 $(u, v, f(u, v) - c(u, v), 0)$。

退流小于 $c(u, v)$，反向边，容量为 $c(u, v)$ 费用为 $1$。

如何保证流量守恒，很显然总共的流在原来的地方是不变的，所以我们考虑计算出每个点流入和流出的差值，然后像上下界网络流一样建边即可。

> [P4003 无限之环](https://www.luogu.com.cn/problem/P4003)
>
> 题面复杂。

[Legendgod’s Blog 题解](https://legendgod.ml/2022/04/14/lg-solution-p4003/)

> [[SDOI 2013] 刺客信条](https://www.luogu.com.cn/problem/P3296)
>
> 给定一棵 $n$ 个点的无根数和一个长度为 $n$ 的 $01$ 串，以及一个长度为 $n$ 的目标 $01$串，每次操作可以翻转当前 $01$ 串的一位，最终要使得存在一种数的自同构排列，使得排列后当前 $01$ 串变成目标串。

[Legendgod’s Blog 题解](https://legendgod.ml/2022/04/14/lg-solution-p3296/)

下面就是比较通用的思路了。

#### 优化建图

> [P2065 [TJOI2011]卡片](https://www.luogu.com.cn/problem/P2065)
>
> 有一个二分图，左右分别 $n, m$ 个点，一个左部点和一个右部点有边当且仅当点权不互质，求最大匹配。
>
> $n, m \le 500$

考虑点权不互质就是至少有一个公因子，考虑中间放一个公因子之后来连接两边这样，这样边数就会变成 $O((n + m) \omega(V))$。

之后跑网络流即可。

> [P5331 [SNOI2019]通信](https://www.luogu.com.cn/problem/P5331)
>
> 给 $n$ 个点排成一列，对于权值为 $a_i$ 的 $i$ 点或者选择支付 $w$ 的代价，或者选择一个 $j <i$ 然后支付 $|a_i - a_j|$ 的代价，对于每个 $j$ 只能被选择一次，求最小代价和。

先考虑朴素的网络流，考虑当前是否被选择了。

- $s \to i \to t$ 表示直接连接。
- $s \to i \to j' \to t$ 表示间接连接，限制 $j' \to t$ 的流量。

这样边数是 $O(n^2)$ 的。

发现连接肯定是一个前缀考虑建立虚点来连接，考虑费用怎么计算。

考虑将权值排序之后相互连接，之后将每个点连接到对应权值的点上。

这样边数就是 $O(n \log n)$ 级别的了。

#### 拆贡献

> [P2050 [NOI2012] 美食节](https://www.luogu.com.cn/problem/P2050)
>
> 有 $n$ 种菜和 $m$ 个厨师，第 $i$ 个厨师做**一道**第 $j$ 种菜时间为 $t_{i, j}$，第 $i$ 种菜有 $p_i$ 个同学想要，每个厨师同时只能做一道菜，问所有同学的最小等待时间和。
> 

首先可以考虑每个厨师做第 $i$ 道菜的贡献，考虑让 $S$ 连接所有的菜，之后考虑厨师做第 $i$ 个菜的贡献，显然这个贡献是有凸性的，因为边数过多所以我们可以考虑对于每次做了菜的厨师再增加其做下一道菜的费用的边。这样点数就被控制在 $\sum p_i$ 中了。

> 注意这样是不能使用 $\tt dijsktra$ 的。
> 

> [P2570 [ZJOI2010]贪吃的老鼠](https://www.luogu.com.cn/problem/P2570)
>
> 有 $n$ 个奶酪和 $m$ 个老鼠，第 $i$ 个奶酪大小 $p_i$，第 $i$ 个老鼠吃奶酪的速度是 $v_i$，同一时刻一个老鼠只能吃一个奶酪，一个奶酪只能被一个老鼠吃，每个奶酪有出现的时间区间 $[l_i, r_i]$，现在可以将所有 $r_i$ 都增加一个量，求最少要增加多少可以使得所有奶酪都被吃完。
> 

二分答案转成判定性问题。

我们先思考最朴素的建图，源点向奶酪连边，容量为奶酪大小，之后将老鼠按照离散化后的时间段拆点，每个时间段向对应的可以吃的奶酪连边，容量为老鼠的 $v \times tim$。

> 考虑还有什么条件是不满足的，可能会出现一块奶酪同一时刻被多只老鼠吃，显然可能存在奶酪大小为 $p_i$ 有 $\max v \times tim < p_i$ 但是在该时段流满的情况。

那么我们需要限制每个时段对于每个奶酪的流，按照老鼠的速度排序，不妨考虑当前时刻第一个吃当前奶酪的老鼠为 $i$ 显然我们需要限制 $\forall j, j > i$ 的老鼠不吃，考虑每个时刻只能使用一个 $v$，但是我们不能表示该限制我们只能表示一段区间的 $v$ 同时被使用了，考虑将 $v$ 表示成前缀的形式也就是差分。

考虑一种神奇的建图：

- 首先将老鼠吃的速度排序之后差分，设第 $i$ 只老鼠的速度为 $\sum_{j = 1} ^ iu_j$。
- 在每个时间段对于每个 $u_i$ 建点，与每个奶酪连权值为 $u_i \times tim$ 容量的边（$tim$ 是时间长度），这样可以保证一个奶酪的出边不会超过老鼠的限制。
- 每个 $u_i$ 对应的点向汇点连 $u_i\times (m-i+1)\times tim$ 容量的边，相当于限制了每个值域前缀的总消耗量。

---

目前图论二分图和网络流部分就已经学完了，还少一块模拟费用流之后会补上的。

正好 $20000$ 字。