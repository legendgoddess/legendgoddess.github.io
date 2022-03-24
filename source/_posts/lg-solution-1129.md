---
title: "P1129 [ZJOI2007] 矩阵游戏 题解"
date: 2022-3-24 12:52:00
tags:
  - 图论
  - 二分图
categories:
  - 洛谷题解
---

[[ZJOI2007] 矩阵游戏 - 洛谷](https://www.luogu.com.cn/problem/P1129)

不能说我会做，只能说这个题目是我根据套路猜出来的。

众所周知我们会考虑将行和列拆开进行计算，我们考虑这个题目，最终的要求就是第 $(i, i)$ 位置是黑色的，也就是第 $i$ 行和第 $i$ 列恰好有一个黑色的匹配。

那么对于一个最终状态就是有 $n$ 个匹配，我们猜测只要有 $n$ 个匹配的都是合法的状态。

考虑左边是行右边是列，这个就是一个二分图，对于这个二分图考虑进行交换某两行的操作，相对行不变可以发现对应的列进行了交换。

那么对于最终的答案我们可以通过交换某两行达到二分图上列的交换，同理行也是可以进行交换。

根据题目交换明显是可逆的，所以这个就是充要条件。

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

const int maxn = 2e5 + 5;
int n, m, mt[maxn], vis[maxn], head[maxn], cnt(1);
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

int dfs(int p) {
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(vis[to]) continue;
        vis[to] = 1;
        if(!mt[to] || dfs(mt[to])) {
            mt[to] = p, mt[p] = to;
            return true;
        }
    }
    return false;
}

void Solve() {
    int i, j;
    cnt = 1;
    r1(n);
    for(i = 1; i <= n * 2; ++ i) head[i] = mt[i] = 0;
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= n; ++ j) {
            int x; r1(x);
            if(!x) continue;
            add(i, j + n), add(j + n, i);
        }
    }
    int ans(0);
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= 2 * n; ++ j) vis[j] = 0;
        if(!mt[i] && dfs(i))
            ++ ans;
    }
//    printf("ans = %d\n", ans);
    if(ans == n) puts("Yes");
    else puts("No");
}

signed main() {
	int i, j, T;
    r1(T); while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
