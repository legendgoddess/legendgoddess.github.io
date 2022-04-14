---
title: "P4003 无限之环 题解"
date: 2022-4-14 8:48:00
tags:
  - 网络流
  - 图论
categories:
  - 洛谷题解
---

[P4003 无限之环](https://www.luogu.com.cn/problem/P4003)

如果直接来做其实一点头绪都没有，于是我们可以点开 [Infinity Loop](https://poki.com/en/g/infinity-loop)

玩了一会。

玩的过程中发现一个明显的性质不管最终答案是什么样的，每个插头都是被另外一个插头配对的。

那么本质上就是每个格子的 $4$ 个位置有若干个是插头，每个插头需要被配对，每次旋转需要代价，求最小代价使得所有插头被配对。

显然对于一个插头我们被配对当且仅当其和 $1$ 个另外的插头配对。

我们考虑拆点限制这个 $1$，考虑使用费用流。

我们可以考虑对于每个格子拆成 $5$ 个点，一个点来限制其他点的 $1$ 次流量，然后剩下 $4$ 个点表示插头，因为需要有源点和汇点我们不妨仿照[骑士共存问题](https://www.luogu.com.cn/problem/P3355)进行染色。

考虑旋转是怎么表示的，本质上就是看这个 $1$ 的流流到哪里去，就是简单连边。

- 一个插头，相邻位置费用为 $1$，对面为 $2$。
- $\tt L$ 形插头，考虑最上面的位置顺时针旋转 $1$ 次可以使最下面插头合法，所以是对面连边费用为 $1$。费用为 $2$ 的情况可以不考虑，因为本质上就是两次费用为 $1$ 的旋转。
- 三个插头，可以通过一次相邻旋转或者一次相对旋转，费用为 $1, 2$。

之后直接使用费用流即可，代码很好写。

> 其实我写法常数可以不用那么大的，但是他短而且不用讨论呀！
>
> 费用流使用了顶标费用流，其中 $\tt dijsktra$ 使用了配对堆优化，复杂度卡得比较满，但是对于图层数比较少的情况可以使用 $\tt dinic$ 快速增广。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace __gnu_pbds;
using namespace std;

namespace Legendgod {
	namespace Read {
		#define Fread
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
const int maxn = (2e3) * 5 + 10;
const int inf = 0x3f3f3f3f;
int n, m, S, T;
struct Edge {
    int to, next;
    int w, c;
}edg[maxn * 20];
int head[maxn], cnt(1);
void add1(int u,int v,int w,int c) {
    edg[++ cnt] = (Edge) {v, head[u], w, c}, head[u] = cnt;
}

void add(int u,int v,int w,int c) {
    add1(u, v, w, c), add1(v, u, 0, -c);
}

int h[maxn], dis[maxn], pre[maxn];

bool vis[maxn];

void Spfa() {
    memset(h, 0x3f, 4 * (T + 2));
    static queue<int> q; while(!q.empty()) q.pop();
    h[S] = 0, vis[S] = 1;
    q.push(S);
    while(!q.empty()) {
        int u = q.front(); q.pop();
//        printf("u = %d\n", u);
        vis[u] = 0;
        for(int i = head[u];i;i = edg[i].next) if(edg[i].w) {
            int to = edg[i].to;
            if(h[to] > h[u] + edg[i].c) {
                h[to] = h[u] + edg[i].c;
                if(!vis[to]) vis[to] = 1, q.push(to);
            }
        }
    }
}

struct Node {
    int id, dis;
    int operator < (const Node &z) const {
        return dis > z.dis;
    }
};

int Dij() {
    memset(dis, 0x3f, 4 * (T + 2));
    memset(vis, 0, sizeof(vis));
    memset(pre, 0, 4 * (T + 2));
    dis[S] = 0;
    static __gnu_pbds::priority_queue<Node> q; while(!q.empty()) q.pop();
    q.push({S, 0});
    while(!q.empty()) {
        int u = q.top().id; q.pop();
        if(vis[u]) continue;
        vis[u] = 1;
        for(int i = head[u];i;i = edg[i].next) {
            int to = edg[i].to; int w = edg[i].w, c = edg[i].c;
            if(w <= 0) continue;
            int tmp = dis[u] + h[u] - h[to] + c;
            if(dis[to] > tmp) {
                dis[to] = tmp;
                pre[to] = i;
                q.push({to, dis[to]});
            }
        }
    }
//    printf("T = %d\n", dis[T]);
    return dis[T] < inf;
}

pair<int, int> Solve() {
    int x(0), y(0);
//    puts("SSs");
    Spfa();
//    puts("SSs");
    while(Dij()) {
        int ansf(inf), ansc(0);
        for(int i = T; i != S; i = edg[pre[i] ^ 1].to)
            ansf = min(ansf, edg[pre[i]].w);
        for(int i = T; i != S; i = edg[pre[i] ^ 1].to) {
            edg[pre[i]].w -= ansf, edg[pre[i] ^ 1].w += ansf;
            ansc += ansf * edg[pre[i]].c;
        }
        x += ansf, y += ansc;
        for(int i = 1; i <= T; ++ i) if(dis[i] < inf) h[i] += dis[i];
    }
    return {x, y};
}

int mf[4] = {3, 4, 1, 2};// 1 2 3 4
int d[4][2] = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
int Sm, sumk(0);
int id(int x,int y,int c) {
    return (x - 1) * m + y + c * Sm;
}
int a[2005][2005];

void build(int x,int y) {
    int op = (x + y) & 1;
    if(op) {
        add(S, id(x, y, 0), inf, 0);
        for(int i = 0; i < 4; ++ i) {
            int nx = x + d[i][0], ny = y + d[i][1];
            if(nx < 1 || nx > n || ny < 1 || ny > m) continue;
            add(id(x, y, i + 1), id(nx, ny, mf[i]), 1, 0);
        }
    }
    else add(id(x, y, 0), T, inf, 0);
    function<void(int,int,int,int)> vadd;
    vadd = [&](int u,int v,int w,int c) {
        if(op == 1) add(u, v, w, c);
        else add(v, u, w, c);
    };
    int W = a[x][y], ssk(0);
    for(int i = 0; i < 4; ++ i) if((W >> i) & 1) {
        ++ sumk, vadd(id(x, y, 0), id(x, y, i + 1), 1, 0);
        ++ ssk;
//        printf("(%d, %d)\n", x, y);
    }
    int opsi[4] = { 3, 4, 1, 2 };
    if(ssk == 1) {
        int ps = 0; for(int i = 0; i < 4; ++ i) if((W >> i) & 1) { ps = i + 1; break; }
        for(int i = 1; i <= 4; ++ i) if(i != ps && i != opsi[ps - 1]) vadd(id(x, y, ps), id(x, y, i), 1, 1);
        vadd(id(x, y, ps), id(x, y, opsi[ps - 1]), 1, 2);
    }
    else if(ssk == 2) {
        for(int i = 0; i < 2; ++ i) if((W >> i) & 1) if((W >> (i + 2)) & 1) return ;
        for(int i = 0; i < 4; ++ i) if((W >> i) & 1) {
            vadd(id(x, y, i + 1), id(x, y, opsi[i]), 1, 1);
        }
    }
    else if(ssk == 3) {
        int ps(0), nps;
        for(int i = 0; i < 4; ++ i) if(!((W >> i) & 1)) { ps = i + 1; break; }
        nps = opsi[ps - 1];
        for(int i = 0; i < 4; ++ i) if((i + 1 != ps) && (i + 1 != nps))
            vadd(id(x, y, i + 1), id(x, y, ps), 1, 1);
        vadd(id(x, y, nps), id(x, y, ps), 1, 2);
    }
}

signed main() {
//    freopen("fdata.in", "r", stdin);
	int i, j;
    r1(n, m);
    Sm = n * m;
    S = Sm * 5 + 1, T = S + 1;
    for(i = 1; i <= n; ++ i) for(j = 1; j <= m; ++ j) {
        r1(a[i][j]);
        build(i, j);
    }
    auto tmp = Solve();
//    printf("sumk = %d\n", sumk);
//    printf("mxflow = (%lld, %lld)\n", tmp.first, tmp.second);
    if(tmp.first * 2 == sumk) printf("%d\n", tmp.second);
    else puts("-1");
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

