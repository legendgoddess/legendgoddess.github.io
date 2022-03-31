---
title: "P6628 [省选联考 2020 B 卷] 丁香之路"
date: 2022-3-31 13:43:00
tags:
  - 图论
  - 欧拉回路
categories:
  - 省选题解
---

[[省选联考 2020 B 卷] 丁香之路 - 洛谷](https://www.luogu.com.cn/problem/P6628)

> 感觉题目比较神仙。

首先画画图，第一个很明显的就是如果每条需要走的边的点都是单调的，那么答案肯定就是直接按照最近的点连接。根据这样的情况我们发现这个就是一条链。

之后考虑一个点度数比较大的菊花的形式，菊花就会导致我们被迫走回头路，这个东西不好考虑。鉴于我们可以随意加边，我们不妨将回头路看成一条新的边，这样我们最终的答案就是每条边都只经过了一次。

> 每条边只经过一次的路径 $\to $ 欧拉路径。

对于欧拉路径的情况我们只需要保证每个节点的度数即可。

对于欧拉路径我们可以让 $S \to T$ 进行连边，这样我们可以不用考虑起始点和结束点究竟连接的是那个点，直接变成欧拉回路。

之后我们考虑对于奇数点进行连边。

但是可能会出现若干个环的情况，我们还是需要考虑图的连通性。直观的想法就是考虑将不同连通块之间连边之后最小生成树。

显然我们贪心考虑相邻连边即可， 为了保证欧拉回路所以最小生成树上的边肯定会经过两次。

> 或者说如果要遍历一遍最小生成树而且回到起点，那么每条边肯定会经过两次。
> 
> 回到起点的原因就是我们将起点和终点连边，他们肯定在一个连通块内。

发现最小生成树上的边是不优的，所以我们要让连通块要尽量连通，对于 $u \to v$ 我们肯定可以拆成 $u \to u + 1 \to u + 2 \to \cdots \to v$ 这个本质是一样的。

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
int n, m, S, deg[maxn], fa[maxn], nf[maxn];

int getfa(int x) { return x == fa[x] ? x : fa[x] = getfa(fa[x]); }
void merge(int u,int v) {
    u = getfa(u), v = getfa(v);
    if(u == v) return ;
    fa[u] = v;
}

typedef long long int64;
int64 sum(0), ans;

struct Node {
    int u, v, w;
    int operator < (const Node &z) const {
        return w < z.w;
    }
}e[maxn];
int etot(0);

signed main() {
	int i, j;
    r1(n, m, S);
    for(i = 1; i <= n; ++ i) fa[i] = i;
    for(i = 1; i <= m; ++ i) {
        int u, v;
        r1(u, v), ++ deg[u], ++ deg[v];
        sum += abs(u - v);
        merge(u, v);
    }
//    printf("sum = %lld\n", sum);
    for(i = 1; i <= n; ++ i) nf[i] = getfa(i);
    for(i = 1; i <= n; ++ i) {
        ans = sum;
        for(j = 1; j <= n; ++ j) fa[j] = nf[j];
        ++ deg[i], ++ deg[S];
        int pre(0);
        for(j = 1; j <= n; ++ j) if(deg[j] & 1) {
            if(!pre) pre = j;
            else {
                ans += j - pre;
                for(int k = pre; k < j; ++ k)
                    merge(k, k + 1);
                pre = 0;
            }
        }
        pre = etot = 0;
        for(j = 1; j <= n; ++ j) if(deg[j]) {
            if(pre && getfa(j) != getfa(pre)) {
                e[++ etot] = { pre, j, j - pre };
            }
            pre = j;
        }
//        for(j = 1; j <= n; ++ j) printf("%d : %d\n", j, getfa(j));
        sort(e + 1, e + etot + 1);
        for(j = 1; j <= etot; ++ j) {
            auto [u, v, w] = e[j];
            if(getfa(u) != getfa(v)) ans += w * 2, merge(u, v);
        }
        -- deg[i], -- deg[S];
        printf("%lld%c", ans, " \n"[i == n]);
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
