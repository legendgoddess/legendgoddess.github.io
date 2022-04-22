---
title: "AT3956 [AGC023E] Inversions 题解"
date: 2022-4-22 8:54:00
tags:
  - 数论
  - 线段树
categories:
  - AT题解
---

[AT3956 [AGC023E] Inversions](https://www.luogu.com.cn/problem/AT3956)

首先来看总方案数怎么算，如果将每个数按照 $a_i$ 排序其排名为 $b_i$，设 $c_i = a_{b_i}$。
$$
f(n) = \prod_{i = 1} ^ n (c_i - i + 1)
$$
考虑对于每个逆序对单独计算贡献，考虑 $i < j, a_i < a_j$。

可以让 $a_j = a_i$ 之后进行计算。
$$
g(i, j) = \frac{1}{2} \times f(n) \times \frac{a_i - b_i}{a_j - b_j + 1} \times \prod_{k = b_i + 1} ^ {b_j - 1} \frac{c_k - k}{c_k - k + 1}
$$
对于 $i > j, a_i < a_j$ 的情况，考虑逆序对不好计算使用补集转化。
$$
f(n) - g(i, j)
$$
那么我们只需要求出 $g(i, j)$ 即可。

考虑对于每个 $j$ 考虑其前缀的贡献，对于 $\dfrac{f(n) \times \dfrac{a_i - b_i}{a_j - b_j + 1}}{2}$ 是固定的。

之后按照排名从小到大加入位置 $p$ 如果其作为 $i$ 那么需要给位置乘上 $a_p - b_p$ 如果是作为 $j$ 那么首先计算贡献，之后全局乘上 $\prod $ 部分的贡献。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

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
constexpr int mod = 1e9 + 7;
int n, m;
int t[maxn << 2], tg[maxn << 2];
struct Seg {
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid ((l + r) >> 1)
    void build(int p,int l,int r) {
        tg[p] = 1, t[p] = 0;
        if(l == r) return ;
        build(ls, l, mid), build(rs, mid + 1, r);
    }
    void pushup(int p) {
        t[p] = (t[ls] + t[rs]) % mod;
    }
    void Add(int p,int c) {
        t[p] = 1ll * t[p] * c % mod, tg[p] = 1ll * tg[p] * c % mod;
    }
    void pushdown(int p) {
        if(tg[p] == 1) return ;
        Add(ls, tg[p]), Add(rs, tg[p]);
        tg[p] = 1;
    }
    void Insert(int p,int l,int r,int pos,int c) {
        if(l == r) return t[p] = c, void();
        pushdown(p);
        if(pos <= mid) Insert(ls, l, mid, pos, c);
        else Insert(rs, mid + 1, r, pos, c);
        pushup(p);
    }
    int Ask(int p,int l,int r,int ll,int rr) {
        if(ll > rr) return 0;
        if(ll <= l && r <= rr) return t[p];
        pushdown(p);
        int res(0);
        if(ll <= mid) res += Ask(ls, l, mid, ll, rr) % mod;
        if(mid < rr) res += Ask(rs, mid + 1, r, ll, rr) % mod;
        return res % mod;
    }
    #undef ls
    #undef rs
    #undef mid
}T1;

struct Seg1 {
    int t[maxn];
    int lowbit(int x) { return x & -x; }
    void add(int p,int c) { for(; p <= n; p += lowbit(p)) t[p] += c; }
    int ask(int p) { int res(0); for(; p > 0; p -= lowbit(p)) res += t[p]; return res; }
}T2;

struct Node {
    int v, id;
    int operator < (const Node& z) const {
        return v == z.v ? id < z.id : v < z.v;
    }
}a[maxn];

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

int Inv(int x) { return ksm(x, mod - 2); }

signed main() {
	int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i].v), a[i].id = i;
    T1.build(1, 1, n);
    sort(a + 1, a + n + 1);
    int fn(1);
    for(i = 1; i <= n; ++ i) fn = 1ll * fn * (a[i].v - i + 1) % mod;
//    printf("fn = %d\n", fn);
    int res(0);
    for(i = 1; i <= n; ++ i) {
        long long tmp(0);
        tmp = T1.Ask(1, 1, n, 1, a[i].id - 1) + mod - T1.Ask(1, 1, n, a[i].id + 1, n);
        tmp %= mod;
        tmp = (1ll * tmp * Inv(2 * (a[i].v - i + 1) % mod) % mod * fn % mod + 1ll * fn * ( T2.ask(n) - T2.ask(a[i].id) ) % mod) % mod;
        res = (res + tmp) % mod;
        T1.Add(1, 1ll * (a[i].v - i) * Inv(a[i].v - i + 1) % mod);
        T1.Insert(1, 1, n, a[i].id, (a[i].v - i));
        T2.add(a[i].id, 1);
    }
    printf("%d\n", res);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

