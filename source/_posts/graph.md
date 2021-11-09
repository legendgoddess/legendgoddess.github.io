---
title: 平面图转对偶图的应用
date: 2021-08-28 10:19:03
tags:
  - 网络流
  - 最短路
categories:
  - 算法
---


### 平面图转对偶图

#### [[BeiJing2006]狼抓兔子](https://darkbzoj.tk/problem/1001)

这个是经典题，显然就是求一个最小割。然后网络流建立无向图即可。

但是如果数据范围没有那么小呢？

众所周知网络流的复杂度其实挺玄学的，而且这又是一张平面图，不妨将其转化为对偶图。

------

定义：

```平面图```：任意两条边不相交的图。

> ```面的次数```：边界的长度，用 $deg(R_i)$ 表示。
>
> ```阶```：几阶就是几个点。

```对偶图```：本质上就是从平面图的面相互连边。

性质：

```1```：平面图的面数量等于对偶图点数量，对偶图面数量等于平面图点数量。

```2```：对偶图一个面的度数是围绕其一周走的边数。

> ![hlTiYF.png](https://img-blog.csdnimg.cn/img_convert/edeb71dd3cb7ff3456362a4909f93bb4.png)

> 显然这张图中间这个面由 $7$ 条边围成，所以其度数是 $7$。

```极大平面图```：如果 $G$ 是简单平面图，而且任意两个点之间加一条新编所得到的图为非平面图，则 $G$ 是极大平面图。

> - 必定联通
> - 任意阶数 $\ge 3$ 的极大平面图中不可能存在割点和桥。

------

> 其实后面还有很多定理，就稍微放一下，之后有能力了再补。

------

两种图互相转换本质上就是讲两个面进行连边而已。

为了方便我们需要将整个网格图划分成两半分别属于 $S, T$。之后只需要跑 $S \to T$ 的最短路即可。

**注意：**

至于连边方面，如果是无向边那显然没事。如果是有向边考虑顺时针旋转 $90$ 度，这个是以右上角为 $S$ 的情况。

对于一个面我们不妨钦定一个表示法，对于普通的网格图可以使用左上角。

但是像这种图，我们不妨考虑是最上角点的编号 $\times 2$，然后根据最后一位是否为 $1$ 来区分一个 $4$ 联通块中的两个面。

本题的代码：

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
//#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
#define getchar gc
#endif // Fread

template <typename T>
void r1(T &x) {
	x = 0;
	char c(getchar());
	int f(1);
	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
	x *= f;
}

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 2e6 + 5;
const int maxm = 1e3 + 5;

int head[maxn], cnt;
struct Edge {
    int to, next, w;
}edg[maxn * 4];
void Add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}

void add(int u,int v,int w) {
    Add(u, v, w), Add(v, u, w);
}

int S = 2000000 + 2,  T = 2000000 + 1, n, m;
struct Node {
    int id, val;
    int operator < (const Node &z) const {
        return val > z.val;
    }
};
priority_queue<Node> q;

bool vis[maxn];
int d[maxn];

void DJ(int st) {
    memset(vis, 0, sizeof(vis));
    q.push((Node) {st, 0}); d[st] = 0;
    while(!q.empty()) {
        Node u = q.top(); q.pop();
        if(vis[u.id]) continue;
        vis[u.id] = 1;
        for(int i = head[u.id];i;i = edg[i].next) {
            int to = edg[i].to;
            if(d[to] > d[u.id] + edg[i].w) {
                d[to] = d[u.id] + edg[i].w;
                if(!vis[to]) q.push((Node) {to, d[to]});
            }
        }
    }
}

int a[maxm][maxm], b[maxm][maxm], c[maxm][maxm];

signed main() {
//    freopen("5.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    memset(d, 0x3f, sizeof(d));
    r1(n, m);
    for(i = 1; i <= n; ++ i) for(j = 1; j < m; ++ j) r1(a[i][j]);
    for(i = 1; i < n; ++ i) for(j = 1; j <= m; ++ j) r1(b[i][j]);
    for(i = 1; i < n; ++ i) for(j = 1; j < m; ++ j) r1(c[i][j]);
    if(n == 1 || m == 1) {
        if(m == 1) for(i = 1; i < n; ++ i) d[T] = min(d[T], b[i][1]);
        if(n == 1) for(i = 1; i < m; ++ i) d[T] = min(d[T], a[1][i]);
    }
    for(i = 1; i < n; ++ i) {
        for(j = 1; j < m; ++ j) {
            int ct((i - 1) * (m - 1) + j); ct *= 2;
//            printf("ct = %d\n", ct);
            add(ct - 1, ct, c[i][j]);
            if(j != m - 1) add(ct - 1, ct + 2, b[i][j + 1]);
            if(i != n - 1) add(ct, ct + (m - 1) * 2 - 1, a[i + 1][j]);
        }
    }
//    puts("ZZZ");
    for(i = 1; i < n; ++ i) add( ((i - 1) * (m - 1) + 1)* 2, T, b[i][1]), add(S, 2 * i * (m - 1) - 1, b[i][m]);
    for(i = 1; i < m; ++ i) add(S, 2 * i - 1, a[1][i]), add(2 * ((n - 2) * (m - 1) + i), T, a[n][i]);
    DJ(S);
    printf("%d\n", d[T] == 0x3f3f3f3f ? 0 : d[T]);
	return 0;
}

```

------

练习题：[P2046 [NOI2010] 海拔](https://www.luogu.com.cn/problem/P2046)。

网格图，直接连边。然后将原来的边顺时针旋转 $90$ 度就能得到两个面如何连边了。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
//#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
#define getchar gc
#endif // Fread

template <typename T>
void r1(T &x) {
	x = 0;
	char c(getchar());
	int f(1);
	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
	x *= f;
}

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 1e6 + 5;
const int maxm = maxn << 1;

struct Node {
    int id;
    long long x;
    int operator < (const Node &z) const {
        return x > z.x;
    }
};

int head[maxn], cnt;
struct Edge {
    int to, next, w;
}edg[maxn << 1];

void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) { v, head[u], w }, head[u] = cnt;
}

priority_queue<Node> q;
long long d[maxn];
bool vis[maxn];
int T, S;

void DJ(int st) {
    memset(d, 0x3f, sizeof(d)), memset(vis, 0, sizeof(vis));
    d[st] = 0; q.push((Node) {st, 0});
    while(!q.empty()) {
        Node u = q.top(); q.pop();
        if(vis[u.id]) continue;
        vis[u.id] = 1;
        for(int i = head[u.id];i;i = edg[i].next) {
            int to = edg[i].to;
            if(d[to] > d[u.id] + edg[i].w) {
                d[to] = d[u.id] + edg[i].w;
                if(!vis[to]) q.push((Node){ to, d[to] });
            }
        }
    }
}

int n;

int id(int x,int y) {
    return (x - 1) * n + y;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n); ++ n;
    T = n * n + 1;
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j < n; ++ j) {
            int x; r1(x);
            if(i == 1) add(S, id(i, j), x);
            else if(i == n) add(id(i - 1, j), T, x);
            else add(id(i - 1, j), id(i, j), x);
        }
    }
    for(i = 1; i < n; ++ i) {
        for(j = 1; j <= n; ++ j) {
            int x; r1(x);
            if(j == 1) add(id(i, j), T, x);
            else if(j == n) add(S, id(i, j - 1), x);
            else add(id(i, j), id(i, j - 1), x);
        }
    }
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j < n; ++ j) {
            int x; r1(x);
            if(i == 1) add(id(i, j), S, x);
            else if(i == n) add(T, id(i - 1, j), x);
            else add(id(i, j), id(i - 1, j), x);
        }
    }
    for(i = 1; i < n; ++ i) {
        for(j = 1; j <= n; ++ j) {
            int x; r1(x);
            if(j == 1) add(T, id(i, j), x);
            else if(j == n) add(id(i, j - 1), S, x);
            else add(id(i, j - 1), id(i, j), x);
        }
    }
    DJ(S);
    printf("%lld\n", d[T]);
	return 0;
}
```




