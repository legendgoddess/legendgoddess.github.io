---
title: CF1633E Spanning Tree Queries 题解 
date: 2022-3-7 20:44:20
tags:
  - 最小生成树
  - 图论
categories:
  - CF题解
---

[Problem - 1633E - Codeforces](https://codeforces.com/problemset/problem/1633/E)

首先发现询问次数是很多的，但是点数和边数是很少的。

所以我们总共有两个方向可以走一个是优化生成树，但是可以发现不管是 $\tt P, K, B$ 这些算法都是不行的，我们只能考虑另一个方法**减少求生成树的次数**。

比较浅层来说可以发现我们可以对于边进行排序，那么如果两个询问 $x_1, x_2$ 其在两条边 $w_1, w_2$ 之间，我们设边的中点为 $\tt mid = \dfrac{w_1 + w_2}{2}$ 那么如果其在中点的同一边那么就不需要再进行操作了。

我们可以通过这个将所有的边都进行如上操作，之后离线询问即可。

```cpp
#include <bits/stdc++.h>
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
#define int long long
const int maxn = 300 + 5;
const int maxnn = 300 + 5;
int n, m;
int E[maxnn * maxnn], tot(0), Edd[maxnn];
const int maxm = 1e7 + 5;
int K, q[maxm];

void init() {
    int p, a, b, c, i;
    r1(p, K, a, b, c);
    for(i = 1; i <= p; ++ i) r1(q[i]);
    for(i = p + 1; i <= K; ++ i) {
        q[i] = (1ll * q[i - 1] * a % c + b) % c;
    }
}

struct Node {
    long long fu, zn, lf;
    Node(int a = 0,int b = 0,int c = 0) : fu(a), zn(b), lf(c) {}
    int operator < (const Node &z) const {
        return lf < z.lf;
    }
}last;
struct Node1 {
    int u, v, w;
}E1[maxn];

int lps = -1;

int fa[maxn], siz[maxn];
int getfa(int x) { return x == fa[x] ? x : fa[x] = getfa(fa[x]); }
bool merge(int u,int v) {
    int s1 = getfa(u), s2 = getfa(v);
    if(s1 == s2) return false;
    if(siz[s1] > siz[s2]) swap(s1, s2);
    siz[s2] += siz[s1];
    fa[s1] = s2;
    return true;
}

long long Ask(long long x) {
    int i, j;
    int tmp = lower_bound(E + 1, E + tot + 1, x) - E;
    if(tmp == lps) {
        long long rs = - last.fu * x + last.zn * x + last.lf;
        return rs;
    }
    else lps = tmp;
    for(i = 1; i <= n; ++ i) fa[i] = i, siz[i] = 1;
    sort(E1 + 1, E1 + m + 1, [&](const Node1 &a, const Node1 &b) {
            return abs(a.w - x) < abs(b.w - x);
    });
    last = Node();
    for(i = 1; i <= m; ++ i) {
        if(merge(E1[i].u, E1[i].v)) {
            if(E1[i].w <= x) last.zn ++, last.lf -= E1[i].w;
            else last.fu ++, last.lf += E1[i].w;
        }
    }
    long long rs = - last.fu * x + last.zn * x + last.lf;
    return rs;
}

void Solve() {
    int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v, w;
        r1(u, v, w);
        Edd[i] = w;
        E[++ tot] = w;
        E1[i].u = u, E1[i].v = v, E1[i].w = w;
    }

    for(i = 1; i <= m; ++ i) {
        for(j = i + 1; j <= m; ++ j) {
            E[++ tot] = (Edd[i] + Edd[j]) >> 1;
        }
    }
    sort(E + 1, E + tot + 1);
    tot = unique(E + 1, E + tot + 1) - E - 1;
    init();
    sort(q + 1, q + K + 1);
    long long ans(0);
    for(i = 1; i <= K; ++ i) {
        ans ^= Ask(q[i]);
    }
    printf("%lld\n", ans);
}

signed main() {
	int i, j, T(1);
//    r1(T);
    while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }

```
