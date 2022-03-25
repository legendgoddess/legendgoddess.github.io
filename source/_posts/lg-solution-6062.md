---
title: "[USACO05JAN]Muddy Fields G 题解"
date: 2022-3-24 18:51:00
tags:
  - 二分图
  - 图论
categories:
  - USACO题解
---

[[USACO05JAN]Muddy Fields G](https://www.luogu.com.cn/problem/P6062)

本质上就是求每个点的最小点覆盖，考虑到一个木板可以覆盖横着或者竖着，可以考虑对于可以被同一个木板覆盖的位置可以缩点成同一个。

> 因为能被同一个木板覆盖肯定比分成两块木板覆盖优。

对于行和列分别进行操作，我们考虑对于图上每个点对于行列的限制是什么。

如果这个行被选择了那么对应的列是不能覆盖到当前位置的。

这个本质就是一个**匹配**，我们求的是最少要多个点能将全部的行和列被覆盖，本质上就是最大独立集，也就是二分图的最大匹配。

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
int n, m, vis[maxn], mt[maxn];
int head[maxn], cnt(1);
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}
int dfs(int p) {
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(!vis[to]) {
            vis[to] = 1;
            if(!mt[to] || dfs(mt[to])) {
                mt[to] = p, mt[p] = to;
                return true;
            }
        }
    }
    return false;
}

char s[55][100];
int ph[55][55], pl[55][55], toth(0), totl(0);

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) scanf("%s", s[i] + 1);
    toth = totl = 0;
    int fl;
    for(i = 1; i <= n; ++ i) {
        fl = 1;
        for(j = 1; j <= m; ++ j) {
            if(fl == 1 && s[i][j] == '*') ++ toth, fl = 0;
            if(s[i][j] == '*') ph[i][j] = toth;
            else if(s[i][j] == '.') fl = 1;
        }
    }
    for(j = 1; j <= m; ++ j) {
        fl = 1;
        for(i = 1; i <= n; ++ i) {
            if(fl == 1 && s[i][j] == '*') ++ totl, fl = 0;
            if(s[i][j] == '*') pl[i][j] = totl;
            else if(s[i][j] == '.') fl = 1;
        }
    }
//    for(i = 1; i <= n; ++ i) for(j = 1; j <= m; ++ j) printf("%d%c", ph[i][j], " \n"[j == m]);
//    puts("\n --- \n");
//    for(i = 1; i <= n; ++ i) for(j = 1; j <= m; ++ j) printf("%d%c", pl[i][j], " \n"[j == m]);
    for(i = 1; i <= n; ++ i) for(j = 1; j <= m; ++ j) if(s[i][j] == '*') {
        add(ph[i][j], pl[i][j] + toth);
        add(pl[i][j] + toth, ph[i][j]);
    }
    int ans(0);
    for(i = 1; i <= toth; ++ i) {
        for(int j = 1; j <= toth + totl; ++ j) vis[j] = 0;
        if(!mt[i] && dfs(i)) ++ ans;
    }
//    for(i = 1; i <= toth; ++ i) printf("%d : %d\n", i, mt[i]);
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


