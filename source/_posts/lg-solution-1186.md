---
title: "P1186 玛丽卡 题解"
date: 2022-3-17 19:56:00
tags:
  - 数据结构
  - 并查集
  - 图论
  - 最短路
categories:
  - 洛谷题解
---

[P1186 玛丽卡 - 洛谷](https://www.luogu.com.cn/problem/P1186)

容易想到一个很明显的做法，就是考虑随便找出一条最短路，之后考虑删除掉任意一条边，再进行计算，答案取最大值。

但是这个复杂度明显是有问题的，我们考虑这个过程我们在干什么。

本质上就是对一条路径进行更新，或者说考虑对于 $A\to B \to C \to D \to E$ 的路径，我们删除掉 $(B, C)$，让路径变成 $A \to B \to X \to Y \to C \to D \to E$，这样更新。

我们思考，我们本质在做的就是在删掉一条边的时候，尝试求次短路，那么我们可以考虑对于每条不是最短路里的边分别进行更新，考虑求出从 $1 \to n$ 和 $n \to 1$ 的两个数组分别为 $d_1, d_n$ 那么我们可以通过 $d_1 + d_n + w$ 进行更新一段路径。

对于我们当前找到的点对 $(u, v)$ 我们考虑其最优的肯定是通过最短路算法得出的路径，我们设 $pre(i)$ 表示点 $i$ 在最短路算法中得到的前驱，那么我们 $(u, v)$ 进行的更新本质上就是对于 $u, v$ 分别考虑，一直跳 $\tt pre$ 直到和最短路径的某个端点重合，可以证明这条路径肯定是最优秀的。

维护的话就是使用并查集和线段树，更新的时候本质就是取区间最小值。

证明：

- 对于一条边的情况肯定是正确的。

对于一段链的情况，考虑更新的条件：如果说还有一条链可以更新当前段而且比当前链更优。

- 如果说他们有重合部分的话，那么显然不会漏掉。

- 如果说是不同的两条链那么当前的边本质上不需要考虑另外的链。

- 如果是同一条边，但是链的两端是不同的，那么这个就不符合最短路的条件，所以不可能出现。

综上我们更新到了所有会产生贡献的边，所以答案肯定是正确的。

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

const int maxn = 1e3 + 5;
int n, m;
int head[maxn], cnt(1);
struct Edge {
    int to, next, w;
}edg[maxn * maxn];
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}

struct Node {
    int id, dis;
    Node(int a = 0,int b = 0) : id(a), dis(b) {}
    int operator < (const Node &z) const {
        return dis > z.dis;
    }
};

bitset<maxn> vis;
int pre[maxn];
void Dj(int st, int *d) {
    static priority_queue<Node> q;
    while(!q.empty()) q.pop();
    for(int i = 1; i <= n; ++ i) d[i] = 0x3f3f3f3f, pre[i] = 0;
    vis.reset();
    d[st] = 0;
    q.push(Node(st, 0));
    while(!q.empty()) {
        int u = q.top().id; q.pop();
        if(vis[u]) continue;
        vis[u] = 1;
        for(int i = head[u];i;i = edg[i].next) {
            int to = edg[i].to, w = edg[i].w;
            if(d[u] + w < d[to]) {
                d[to] = d[u] + w;
                pre[to] = u;
                if(!vis[to]) q.push(Node(to, d[to]));
            }
        }
    }
}

int d[2][maxn];
const int maxm = (maxn << 2);
struct Seg {
    int t[maxm], tag[maxm], md[maxm];
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid md[p]
    void build(int p,int l,int r) {
        tag[p] = 0x3f3f3f3f, t[p] = 0x3f3f3f3f;
        if(l == r) return ;
        md[p] = (l + r) >> 1;
        build(ls, l, mid), build(rs, mid + 1, r);
    }
    void padd(int p,int c) {
        t[p] = min(t[p], c), tag[p] = min(tag[p], c);
    }
    void pushdown(int p) {
        if(tag[p] != 0x3f3f3f3f) {
            padd(ls, tag[p]), padd(rs, tag[p]);
            tag[p] = 0x3f3f3f3f;
        }
    }
    void pushup(int p) { t[p] = min(t[ls], t[rs]); }
    void Add(int p,int l,int r,int ll,int rr,int c) {
        if(ll > rr) return ;
        if(ll <= l && r <= rr) return padd(p, c);
        pushdown(p);
        if(ll <= mid) Add(ls, l, mid, ll, rr, c);
        if(mid < rr) Add(rs, mid + 1, r, ll, rr, c);
        pushup(p);
    }
    int Ask(int p,int l,int r,int pos) {
        if(l == r) return t[p];
        pushdown(p);
        if(pos <= mid) return Ask(ls, l, mid, pos);
        else return Ask(rs, mid + 1, r, pos);
    }
}T;

int pos[maxn], fa[maxn], vc[maxn][maxn];

int getfa(int x) { return x == fa[x] ? x : fa[x] = getfa(fa[x]); }

signed main() {
	int i, j;
	memset(vc, 0x3f, sizeof(vc));
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v, w; r1(u, v, w);
        add(u, v, w), add(v, u, w);
        vc[u][v] = vc[v][u] = w;
    }
    Dj(n, d[1]);
    Dj(1, d[0]);
    for(i = 1; i <= n; ++ i) fa[i] = pre[i];// 直接连边
    m = 0;
    for(i = n; i; i = pre[i]) {
        pos[i] = ++ m;
        fa[i] = i;// 间接连边， 有用的边重新标号
//        printf("i = %d\n", i);
        if(pre[i]) vc[i][pre[i]] = vc[pre[i]][i] = 0x3f3f3f3f;
    }
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, fa[i]);
    T.build(1, 1, m);
    for(i = 1; i <= n; ++ i) {
        for(j = i + 1; j <= n; ++ j) if(vc[i][j] != 0x3f3f3f3f) {
            int c = min(d[0][i] + vc[i][j] + d[1][j], d[1][i] + vc[i][j] + d[0][j]);
            int L = pos[getfa(i)], R = pos[getfa(j)];
            if(L > R) swap(L, R);
//            printf("c = %d, [%d, %d]\n", c, i, j);
            T.Add(1, 1, m, L + 1, R, c);
        }
    }
    int ans(0);
    for(i = 2; i <= m; ++ i) ans = max(ans, T.Ask(1, 1, m, i));
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


