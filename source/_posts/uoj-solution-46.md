---
title: "#46. [清华集训2014]玄学 题解"
date: 2022-3-18 14:18:00
tags:
  - 二进制分组
  - 归并排序
  - 线段树
categories:
  - UOJ题解
---

[【清华集训2014】玄学 - 题目 - Universal Online Judge (uoj.ac)](https://uoj.ac/problem/46)

首先暴力很明显，要么是模拟，要么直接树套树来搞。

考虑我们必须要维护时间轴，我们考虑对于区间能否进行一些操作。

考虑区间 $[l, r]$ 我们将其考虑一个位置 $pos$ 能取到什么，因为该操作在 $[1, l-1], [l, r], [r+1,n]$ 的贡献本质是一样的，我们考虑将区间的右端点作为代表点，查询的时候直接二分一个最接近的右端点即可。

这样我们发现我们只需要在叶子节点进行上述操作，但是区间查询呢？

我们考虑二进制分组的思想，考虑对于两个时间 $a, b, a < b$ 如果说当前满足 $a$ 肯定满足 $b$，我们考虑通过这种情况进行归并即可。

插入的复杂度是 $O(\log n)$ 的，查询是 $O(\log^2 n)$ 的。

> 话说之后还要学习一下二进制分组。

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
const int maxk = 1e7 + 5;
int n, m;
const int maxm = maxn << 2;
int L[maxm], R[maxm], tot(0);
int sa[maxn];
struct Node {
    long long ps, a, b;
}t[maxk];

int ans(0), mod;
const int N = 1e5;

struct Seg {
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid ((l + r) >> 1)
    void modify(int p,int l,int r,int ll,int rr,int a,int b,int pos) {
        if(l == r) {
            L[p] = tot + 1;
            if(ll > 1) t[++ tot] = (Node) {ll - 1, 1, 0};
            t[++ tot] = (Node) {rr, a, b};
//            printf("(%d, %d, %d)\n", rr, a, b);
            if(rr < n) t[++ tot] = (Node) {n, 1, 0};
            R[p] = tot;
            return ;
        }
        if(pos <= mid) modify(ls, l, mid, ll, rr, a, b, pos);
        else modify(rs, mid + 1, r, ll, rr, a, b, pos);
        if(pos < r) return ;
        int i = L[ls], ie = R[ls], j = L[rs], je = R[rs];
        L[p] = tot + 1;
        while(i <= ie && j <= je) {
            t[++ tot] = (Node) {min(t[i].ps, t[j].ps), 1ll * t[i].a * t[j].a % mod, (1ll * t[i].b * t[j].a + t[j].b) % mod};
            if(t[i].ps == t[j].ps) ++ i, ++j;
            else if(t[i].ps < t[j].ps) ++ i;
            else ++ j;
        }
//        printf("tot = %d\n", tot);
        R[p] = tot;
    }

    #undef mid

    void Calc(int x,int K) {
        int l = L[x], r = R[x], res(0);
//        printf("x = %d, (%d, %d)\n", x, l, r);
        while(l <= r) {
            int mid = (l + r) >> 1;
//            printf("ps = %lld\n", t[mid].ps);
            if(t[mid].ps >= K) r = mid - 1, res = mid;
            else l = mid + 1;
        }
//        printf("res = %d\n", res);
        ans = (1ll * ans * t[res].a + t[res].b) % mod;
    }

    void Query(int p,int l,int r,int ll,int rr,int K) {
        if(ll <= l && r <= rr) return Calc(p, K);
        int mid = (l + r) >> 1;
        if(ll <= mid) Query(ls, l, mid, ll, rr, K);
        if(mid < rr) Query(rs, mid + 1, r, ll, rr, K);
    }

    #undef ls
    #undef rs
}T;
int lastans(0);
void Xor(int &a) { a ^= lastans; }

signed main() {
	int i, j, X;
	r1(X);
	X &= 1;
    r1(n, mod);
    for(i = 1; i <= n; ++ i) r1(sa[i]);
    int Q, opsum(0);
    r1(Q);
    while(Q --) {
        int opt, a, b, x, y, K;
        r1(opt);
        if(opt == 1) {
            r1(x, y, a, b);
            ++ opsum;
            if(X == 1) Xor(x), Xor(y);
            T.modify(1, 1, N, x, y, a, b, opsum);
        }
        else {
            r1(x, y, K);
            if(X == 1) Xor(x), Xor(y), Xor(K);
            ans = sa[K];
            T.Query(1, 1, N, x, y, K);
            printf("%d\n", lastans = ans);
        }
    }
//    printf("tot = %d\n", tot);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
