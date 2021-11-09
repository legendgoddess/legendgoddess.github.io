---
title: CF827F Dirty Arkady‘s Kitchen
date: 2021-08-31 11:02:38
tags:
  - 拓扑
  - Dp
categories:
  - CF题解
---

### [CF827F Dirty Arkady's Kitchen](https://codeforces.com/problemset/problem/827/F)
> 说实话这个拆点和拆边都是可以过的。

发现边是可以重复经过的，那么对于两条边只要考虑奇偶性就好了。

我们考虑对于一条边拆成两部分，一个是起点为奇的，另外一个是偶的。

什么时候一条路径是合法的，也就是说对于一条边 $E$，起点到他的路径上肯定存在一条边的上界是 $\ge E_l$ 的。

> 注意这里边是会消失的。

然后考虑加边的顺序，比较显然直接是从小到大进行加边。这样对于我们路径的上界肯定是从拓扑序比起小的边得到的，也就是意味着这条边出现的时候肯定是可以走的。

设 $f(i, 0/1)$ 表示到当前点的奇偶性为 $0/1$ 的最晚时间。

如果说当前时刻这条边是不能走的，我们可以考虑将其加入到当前 $u$ 的边集中，之后更新。

> 因为现在不能走并不意味着不能被其他的边更新。

然后之后更新就是像最短路一样，如果说一条边的终止点在之前被更新过了，那么我们就直接跳过更新。

```cpp
#include <bits/stdc++.h>
using namespace std;

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
const int maxn = 5e5 + 5;
const int maxm = maxn << 1;

struct Node {
    int u, v, l, r, opt;
    Node(int a = 0,int b = 0,int c = 0,int d = 0,int e = 0) : u(a), v(b), l(c), r(d), opt(e) {}
    int operator < (const Node &z) const {
        return l > z.l;
    }
};

priority_queue<Node> q;

vector<Node> vc[maxn][2];
int f[maxn][2];

int n, m;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m), memset(f, -0x3f, sizeof(f));
    if(n == 1) return puts("0"), 0;
    for(i = 1; i <= m; ++ i) {
        int u, v, l, r;
        r1(u, v, l, r); -- r;
        int opt = (r - l) & 1;
        auto Add = [&] (int u,int v,int l,int r) {
            q.push(Node (u, v, l, r, 1));
        };
        Add(u, v, l, r - opt);
        Add(u, v, l + 1, r - (!opt));
    }
    f[1][0] = 0;
    auto Solve = [&] (int u,int v,int l,int r) {
        int opt(l & 1);
        if(f[u][opt] >= l) {
            if(v == n) { printf("%d\n", l + 1); exit(0); }
            if(f[v][!opt] <= r) {
                f[v][!opt] = r + 1;
                for(auto V : vc[v][!opt]) {
                    q.push(Node(V.u, V.v, l + 1, V.r));
                }
            }
        }
        else vc[u][opt].push_back(Node(u, v, l, r));
    };
    while(!q.empty()) {
        Node u = q.top(); q.pop();
        if(u.l > u.r) continue;
        Solve(u.u, u.v, u.l, u.r);
        if(u.opt)
            Solve(u.v, u.u, u.l, u.r);
    }
    puts("-1");
	return 0;
}
```




