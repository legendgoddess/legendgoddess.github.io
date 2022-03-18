---
title: "P5882[CTSC2015]misc 题解"
date: 2022-3-18 13:10:00
tags:
  - 最短路
categories:
  - 洛谷题解
---

考虑题目要求我们求：

$$
R(v) = \sum_{s \ne v \ne t} \frac{a_sa_t sd_{s, t}(v)}{sd(s, t)}
$$

考虑将上面的东西拆掉得到：

$$
R(v) = \sum_{s \ne v \ne t} \frac{a_sa_tsd(s, v)sd(v, t)}{sd(s, t)}
$$

考虑枚举开始点 $s$ 可以得到关于 $s$ 的式子：

$$
R(v) = \sum_{s} a_s \sum_{s \ne v \ne t} \frac{a_tsd(s,v)sd(v, t)}{sd(s, t)}
$$

发现 $n$ 事实上可以让我们跑一遍全源最短路，那么本质上每一条最短路的宽度都是可以处理出来的。

那么我们考虑从后向前进行递推，那么我们的 $t$ 就是结束位置，显然最短路构成的肯定是一个 $DAG$。

设 $f(v) = \sum_t \frac{a_t sd(v, t)}{sd(s,t)}$，剩下的 $sd(s, v)$ 考虑在计算答案的时候乘上。

那么 $f$ 的转移比较显然，考虑加入一个点的时候肯定是直接加成 $\frac{a_t}{sd(s, t)}$，当我们通过一条边进行转移的时候，所有的 $sd(v, t)$ 都会产生变化，因为考虑到宽度是乘法，所有都同时乘上了 $\tt wide(t, t)$。

这样进行转移即可。

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
int n, m;

int head[maxn], cnt(1);
struct Edge {
    int to, next, w;
    long double wd;
}edg[maxn << 1];
void add(int u,int v,int w, long double wd) {
    edg[++ cnt] = (Edge) {v, head[u], w, wd}, head[u] = cnt;
}

int d[maxn], vis[maxn];

struct Node {
    int id, dis;
    int operator < (const Node &z) const {
        return dis > z.dis;
    }
};

long double wd[maxn], ans[maxn], f[maxn];

void Dj(int st) {
    priority_queue<Node> q;
    while(!q.empty()) q.pop();
    q.push((Node) {st, 0});
    memset(d, 0x3f, sizeof(d)), memset(vis, 0, sizeof(vis));
    d[st] = 0, wd[st] = 1;
    while(!q.empty()) {
        int u = q.top().id; q.pop();
        if(vis[u]) continue;
        vis[u] = 1;
//        printf("u = %d\n", u);
        for(int i = head[u];i;i = edg[i].next) {
            int to = edg[i].to;
            if(d[to] > d[u] + edg[i].w) {
                d[to] = d[u] + edg[i].w;
                wd[to] = wd[u] * edg[i].wd;
                q.push((Node) {to, d[to]});
            }
            else if(d[to] == d[u] + edg[i].w)
                wd[to] += wd[u] * edg[i].wd;
        }
    }
}

vector<int> vc[maxn];

int deg[maxn], a[maxn];

void Topu(int st) {
    queue<int> q; memset(f, 0, sizeof(f));
    while(!q.empty()) q.pop();
    for(int i = 1; i <= n; ++ i) if(!deg[i]) q.push(i);

    while(!q.empty()) {
        int u = q.front(); q.pop();
        ans[u] += a[st] * wd[u] * f[u], f[u] += a[u] / wd[u];
//        printf("u = %d\n", u);
        for(auto it : vc[u]) {
            int to = edg[it].to;
//            printf("to = %d\n", to);
            f[to] += f[u] * edg[it].wd;
//            printf("wd = %Lf\n", edg[it].wd);
            -- deg[to];
            if(!deg[to]) q.push(to);
        }
    }
}

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= m; ++ i) {
        int u, v, w;
        long double wd;
        r1(u, v, w);
        scanf("%Lf", &wd);
        add(u, v, w, wd), add(v, u, w, wd);
    }
    for(i = 1; i <= n; ++ i) {
        Dj(i), wd[i] = 0;
        for(int u = 1; u <= n; ++ u) deg[u] = 0;
        for(int u = 1; u <= n; ++ u) {
            for(int j = head[u];j;j = edg[j].next) {
                int to = edg[j].to;
                if(d[to] == d[u] + edg[j].w)
                    vc[to].push_back(j ^ 1), ++ deg[u];
            }
        }
        Topu(i);
        for(int u = 1; u <= n; ++ u) vc[u].clear();
    }
    for(i = 1; i <= n; ++ i) printf("%.6Lf\n", ans[i]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```




