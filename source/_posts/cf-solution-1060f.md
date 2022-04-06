---
title: "CF1060F Shrinking Tree 题解"
date: 2022-4-6 09:49:00
tags:
  - 树
  - Dp
categories:
  - CF题解
---

[Problem - 1060F - Codeforces](https://codeforces.com/problemset/problem/1060/F)

考虑每个数如果被选择为最终的答案，要么是操作没有选择与其相连的边，要么是操作了这个边用 $\frac{1}{2}$ 的概率活下来了。

首先总边数是每次减少 $1$ 的，一个点被选择的概率只和该点周围连接了几条边有关。

设 $f(u, j)$ 表示在 $u$ 的子树中，对于所有删边顺序只对最后 $j$ 条边选择存活节点，根节点的编号仍然是 $u$ 的概率，节点 $u$ 的答案是 $\frac{f(u, n-1)}{(n-1)!}$。

考虑合并 $(u, v)$，我们只需要考虑 $u$ 还有 $v$ 的子树，我们同样设边的状态为 $g(i)$ 和上文相同。

枚举 $(u, v)$ 在什么时候被删除。

如果 $j \le i$ 意味着边收缩完成之后，新的编号已经确定，所以必须选择 $u$ 概率为 $\frac{1}{2}$，同时因为 $(u, v)$ 已经合并，所以最后的 $j -1$ 条边都需要选择 $u$，也就是选择 $v$。转移为： $g_i \leftarrow f(v, j - 1) \times \frac{1}{2}$。

如果 $j > i$ 意味着这条边不需要选择当前边，所以直接转移 $g_i \leftarrow f(v, i)$。

考虑合并子树的答案，考虑之前选择了 $i$ 条边，在当前子树选择了 $j$ 条边，我们需要钦定选择的顺序 $\binom{i + j}{j}$，设之前有 $m$ 条边，当前子树有 $n$ 条边。删边顺序同样需要选择：$\binom{n + m}{n}$。

$$
f(u, i + j) \leftarrow g(j) \times f(u, i) \times \binom{i + j}{j}\binom{n + m}{n}
$$

树上背包复杂度是 $O(n^2)$ ，复杂度瓶颈在于 $g$ 计算，是 $O(n\times n^2)$。

将每个点作为根进行计算，复杂度是 $O(n \times n^3)$。

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

const int maxn = 1e2 + 5;
typedef long double lb;
int n, m, head[maxn], cnt(1);
lb C[maxn][maxn];
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

void init(int x) {
    C[0][0] = 1;
    for(int i = 1; i <= x; ++ i) {
        C[i][0] = 1;
        for(int j = 1; j <= i; ++ j)
            C[i][j] = C[i - 1][j] + C[i - 1][j - 1];
    }
}

int siz[maxn];
lb g[maxn], tmp[maxn], f[maxn][maxn];

void dfs(int p,int pre) {
    f[p][0] = siz[p] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        dfs(to, p);
        memset(g, 0, sizeof(g));
        for(int j = 0; j <= siz[to]; ++ j) {
            for(int ts = 1; ts <= siz[to]; ++ ts) {
                if(j < ts) g[j] += f[to][j];
                else if(j >= ts) g[j] += f[to][ts - 1] * 0.5;
            }
//            printf("(%d, %d) = %.6Lf\n", p, j, g[j]);
        }
        memset(tmp, 0, sizeof(tmp));
        for(int a = 0; a <= siz[to]; ++ a) for(int b = 0; b < siz[p]; ++ b) {
            tmp[a + b] += g[a] * f[p][b] * C[a + b][a] * C[siz[p] - b - 1 + siz[to] - a][siz[p] - b - 1];
//            printf("V = %.6Lf\n", f[to][a]);
        }
        siz[p] += siz[to];
        for(int a = 0; a < siz[p]; ++ a) f[p][a] = tmp[a];
    }
}

signed main() {
	int i, j;
    init(100);
    r1(n);
    for(i = 1; i < n; ++ i) {
        int u, v; r1(u, v), add(u, v), add(v, u);
    }
    dfs(1, 0);
    lb iv(1);
    for(i = 1; i < n; ++ i) iv *= i;
    for(i = 1; i <= n; ++ i) {
        memset(f, 0, sizeof(f));
        dfs(i, 0);
//        for(j = 0; j < n; ++ j) printf("%d : %.6Lf\n", j, f[i][j]);
        printf("%.10Lf\n", f[i][n - 1] / iv);
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
