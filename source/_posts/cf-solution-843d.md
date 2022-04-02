---
title: "CF843D Dynamic Shortest Path 题解"
date: 2022-4-2 14:05:00
tags:
  - 图论
categories:
  - CF题解
---

[Problem - 843D - Codeforces](https://codeforces.com/problemset/problem/843/D)

> 竟然直接被我想对了。

首先这个 $O(nq)$ 貌似是可以过的，可以发现是求最短路问题。所以做法应该比较直观就是考虑每次暴力 $O(n)$ 进行更新。

如何进行更新呢？

考虑原来的最短路，不妨设为 $d(i)$，现在有新增加的路径 $add(i)$。可以发现我们可以直接通过宽搜看一下 $add(i)$ 能否进行更新。

但是我们这样还是需要优先队列，我们考虑如何模拟这个过程，可以发现每次是 $+1$，所以距离最多是增加 $n$。我们直接开桶暴力存即可，如果发现 $add(i)$ 与当前信息不符合可以直接剪枝，复杂度是 $O(n)$ 的。

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
int n, m, Q;
vector<pair<int, int> > vc[maxn];

void add(int u,int v,int i) {
    vc[u].push_back({v, i});
}

typedef long long int64;
const int64 inf = 1e15;
int64 d[maxn], w[maxn];
struct Node {
    int id; int64 dis;
    int operator < (const Node &z) const {
        return dis > z.dis;
    }
};

priority_queue<Node> q;
int vis[maxn];

void Dij() {
    while(!q.empty()) q.pop();
    fill(d + 1, d + n + 1, inf);
    d[1] = 0;
    q.push({1, 0});
    while(!q.empty()) {
        int u = q.top().id; q.pop();
        if(vis[u]) continue;
        vis[u] = 1;
        for(auto i : vc[u]) {
            int to = i.first; int64 wt = w[i.second];
            if(d[to] > d[u] + wt) {
                d[to] = d[u] + wt;
                if(!vis[to]) q.push({to, d[to]});
            }
        }
    }
}

queue<int> buc[maxn];

int64 ad[maxn];

signed main() {
	int i, j;
    r1(n, m, Q);
    for(i = 1; i <= m; ++ i) {
        int u, v;
        r1(u, v, w[i]);
        add(u, v, i);
    }
    Dij();
    for(i = 1; i <= n; ++ i) if(d[i] == inf) d[i] = -1;
//    for(i = 1; i <= n; ++ i) printf("%d : %lld\n", i, d[i]);
    while(Q --) {
        int opt, c;
        r1(opt); if(opt == 1) { r1(c); printf("%lld\n", d[c]); }
        else {
            int c; r1(c);
            for(i = 1; i <= c; ++ i) { int x; r1(x); w[x] ++; }
            fill(ad + 1, ad + n + 1, c + 1);
            buc[ad[1] = 0].emplace(1);
            for(int _ = 0; _ <= c; ++ _) {
                for(int v; !buc[_].empty(); buc[_].pop()) {
                    v = buc[_].front();
                    if(ad[v] != _) continue;
//                    printf("%d : %d\n", _, v);
                    for(auto x : vc[v]) {
                        int to = x.first; int64 wt = w[x.second];
                        int64 tmp = d[v] + ad[v] + wt - d[to];
//                        printf("(%d ,%d) = wt = %lld\n", v, to, wt);
                        if(tmp < ad[to]) buc[ad[to] = tmp].emplace(to);
                    }
                }
            }
            for(i = 1; i <= n; ++ i) if(ad[i] != c + 1) d[i] += ad[i];
        }
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


