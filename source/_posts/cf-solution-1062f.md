---
title: "CF1062F Upgrading Cities 题解"
date: 2022-4-5 09:58:00
tags:
  - 图论
categories:
  - CF题解
---

[Problem - 1062F - Codeforces](https://codeforces.com/problemset/problem/1062/F)

> 可能是题单里面最水的一题了吧。

直接想到拓扑排序，考虑在队列中的所有点都是互相不可到达。考虑使用正反两次拓扑排序来计算答案。

- `q.size() == 1` $x$ 可以到达剩下的所有点。

- `q.size() == 2` 考虑队列中的 $x, y$ 两点显然 $x$ 是不能到达 $y$ 的，如果出现 $y$ 的一个出边 $y \to z$ 且 $z$ 的入度为 $1$，那么 $x$ 就没救了，直接打标记。

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

const int maxn = 3e5 + 5;
int n, m;
int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[maxn << 1];
int dg[maxn];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
    ++ dg[v];
}

queue<int> q;
int f[maxn], vis[maxn];

void Solve(int x,int y,int c) {
    int fl(0);
    for(int i = head[y];i;i = edg[i].next) {
        int to = edg[i].to;
        if(dg[to] == 1) { fl = 1; break; }
    }
    if(fl) vis[x] = 1;
    else f[x] += c;
}

void topu() {
    int i, j;
    int sum(0);
    while(!q.empty()) q.pop();
    for(i = 1; i <= n; ++ i) if(dg[i] == 0) q.push(i), ++ sum;
    while(!q.empty()) {
        int sz = q.size(), u = q.front(); q.pop();
        if(sz == 1) f[u] += n - sum;
        else if(sz == 2) Solve(u, q.front(), n - sum);
        for(i = head[u];i;i = edg[i].next) {
            int to = edg[i].to;
            if(!(-- dg[to])) q.push(to), ++ sum;
        }
    }
}

vector<pair<int, int>> E;

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v; r1(u, v), add(u, v), E.push_back({v, u});
    }
    topu();
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, f[i]);
    memset(head, 0, sizeof(head)), memset(dg, 0, sizeof(dg)), cnt = 1;
    for(const auto& v: E) add(v.first, v.second);
    topu();
    int ans(0);
    for(i = 1; i <= n; ++ i) if(!vis[i] && f[i] >= n - 2) ++ ans;
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


