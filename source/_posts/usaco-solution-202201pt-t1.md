---
title: "P8099 [USACO22JAN] Minimizing Haybales P 题解"
date: 2022-4-27 21:18:00
tags:
  - 平衡树
  - 贪心
categories:
  - USACO题解
---

#### [P8099 [USACO22JAN] Minimizing Haybales P](https://www.luogu.com.cn/problem/P8099)

考虑交换操作会自然想到贪心，考虑贪心选择。

现在对于贪心有一个唯一的问题：是否存在 $i < j, h_i > h_j$ 在 $i$ 进行交换之后 $j$ 不能到达 $j$ 先手操作的最优位置。

考虑 $i$ 向左操作的过程，其可以经过的是 $[h_i - k, h_i + k]$ 的部分，$j$ 经过的是 $[h_j - k, h_j + k]$。

仔细想想确实也没有影响，那么我们可以得到一个贪心。

从左向右扫维护最小字典序的序列，之后二分到一个最大的可以移动的位置，之后再次二分找到最优位置，也就是使得后缀没有 $< h_i$ 的位置。

我们使用平衡树上二分就可以解决这题。

> 一开始我做的时候，我贪心证伪了 $\cdots$
>
> 这样显得我很呆 $\cdots$

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
int n, K;

int ls[maxn], rs[maxn], mn[maxn], mx[maxn], vl[maxn], rd[maxn], sz[maxn], rt, tot;
void pushup(int p) {
    mn[p] = min(min(mn[ls[p]], mn[rs[p]]), vl[p]);
    mx[p] = max(max(mx[ls[p]], mx[rs[p]]), vl[p]);
    sz[p] = sz[ls[p]] + sz[rs[p]] + 1;
}

int news(int v) {
    int x = ++ tot;
    vl[x] = mn[x] = mx[x] = v, sz[x] = 1;
    return rd[x] = rand(), ls[x] = rs[x] = 0, x;
}
int merge(int x,int y) {
    if(!x || !y) return x | y;
    if(rd[x] > rd[y]) return rs[x] = merge(rs[x], y), pushup(x), x;
    else return ls[y] = merge(x, ls[y]), pushup(y), y;
}

void split(int p,int &x,int &y,int K) {
    if(!p) return x = y = 0, void();
    if(K <= sz[ls[p]]) split(ls[p], x, ls[y = p], K);
    else split(rs[p], rs[x = p], y, K - sz[ls[p]] - 1);
    pushup(p);
}

void move(int i,int ed) {
    if(i == ed) return ;
    int a, b, c, d;
    split(rt, c, d, i), split(c, b, c, i - 1), split(b, a, b, ed - 1);
    rt = merge(a, merge(c, merge(b, d)));
}

void print(int x) {
    if(!x) return ;
    print(ls[x]), printf("%d\n", vl[x]), print(rs[x]);
}

void Solve() {
    int i, j;
    r1(n, K);
    mn[0] = 1e9;
    for(i = 1; i <= n; ++ i) {
        int h, p, ln, a, tot(0); r1(h);
        p = rt = merge(rt, news(h));
        while(p) {
            int r = rs[p];
            if(mx[r] - h > K || h - mn[r] > K) p = r;
            else if(vl[p] - h > K || h - vl[p] > K) { tot += sz[r]; break; }
            else tot += sz[r] + 1, p = ls[p];
        }
        if(tot == 1) continue;
//        puts("XX");
        ln = i - tot;
        split(rt, rt, a, ln), p = a, tot = 0;
        if(mx[a] == h) { rt = merge(rt, a); continue; }
        while(p) {
            int l = ls[p];
            if(mx[l] > h) p = l;
            else if(vl[p] > h) { tot += sz[l]; break; }
            else tot += sz[l] + 1, p = rs[p];
        }
        rt = merge(rt, a), move(i, ln + tot + 1);
    }
    print(rt);
}

signed main() {
	int i, j, T(1);
//    r1(T);
    while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

