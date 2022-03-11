---
title: "CF1641C Anonymity Is Important 题解"
date: 2022-3-11 16:20:30
tags:
  - 数据结构
categories:
  - CF题解
---

[Problem - 1641C - Codeforces](https://codeforces.com/problemset/problem/1641/C)

> 一道不是很会的数据结构题。

这里很明显的就是变成 $0$ 的区间是不需要考虑的，我们只需要考虑变成 $1$ 的区间。

这里会有一个问题，如果新加入了一个变成 $0$ 的区间，那么我们之前变成 $1$ 的区间就是有可能产生贡献的，所以我们要不**离线**要么就是通过**数据结构**进行存储。

笔者发现离线一下子不是很好想，所以考虑使用数据结构进行维护，根据上述的条件发现我们不能时刻保证手上都有答案，所以需要再进行询问的时候来计算答案。

不妨考虑进行询问 $x$，显然如果 $x$ 已经被明确说明过没有病直接回答 `NO` 即可，剩下的就是可能有病的情况，考虑什么样的一个区间才可能能确认 $x$ 是有病的，显然就是一个区间只有 $x$ 是不确定的，我们考虑找到这个区间的范围，就是最左边连续的确定没病的位置 $L$ 和最右边的 $R$，我们考虑对于 $1$ 区间的左端点在 $[L, x]$ 之间如果右端点 $\le R$ 那么肯定是合法的。

> 显然右端点肯定不会 $< x$ 不然区间之间就会产生矛盾。

我们通过维护区间最小的右端点即可，使用线段树进行维护，复杂度 $O(n \log n)$。

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

int n, Q;

set<int> wr;
int vl[maxn << 2], md[maxn << 2];
struct Seg {
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid md[p]
    void build(int p,int l,int r) {
        vl[p] = 0x3f3f3f3f;
        if(l == r) return ;
        mid = (l + r) >> 1;
        build(ls, l, mid), build(rs, mid + 1, r);
    }
    void pushup(int p) { vl[p] = min(vl[p], min(vl[ls], vl[rs])); }
    void Insert(int p, int l,int r,int ps,int c) {
        if(l == r) return vl[p] = min(vl[p], c), void();
        if(ps <= mid) Insert(ls, l, mid, ps, c);
        else Insert(rs, mid + 1, r, ps, c);
        pushup(p);
    }
    int Ask(int p,int l,int r,int ll,int rr) {
        if(ll > rr) return 0x3f3f3f3f;
        if(ll <= l && r <= rr) return vl[p];
        int res(0x3f3f3f3f);
        if(ll <= mid) res = min(res, Ask(ls, l, mid, ll, rr));
        if(mid < rr) res = min(res, Ask(rs, mid + 1, r, ll, rr));
        return res;
    }
}T;


signed main() {
	int i, j;
    r1(n, Q);
    T.build(1, 1, n);
    for(i = 1; i <= n; ++ i) wr.insert(i);
    for(int _ = 1; _ <= Q; ++ _) {
        int opt, l, r, x;
        r1(opt);
        if(opt == 0) {
            r1(l, r, x);
            if(x == 0) {
                vector<set<int>::iterator> buc; buc.clear();
                for(auto it = wr.lower_bound(l); it != wr.end() && *it <= r; ++ it)
                    buc.emplace_back(it);
                for(const auto &it : buc) wr.erase(it);
            }
            else T.Insert(1, 1, n, l, r);
        }
        else {
            r1(x);
            auto it = wr.lower_bound(x), it1 = wr.lower_bound(x + 1);
            if(it == wr.end() || *it != x) { puts("NO"); continue; }
            int L, R;
            if(it == wr.begin()) L = 1; else L = *(-- it) + 1;
            if(it1 == wr.end()) R = n; else R = *it1 - 1;
            int tmp = T.Ask(1, 1, n, L, x);
            if(tmp <= R) puts("YES");
            else puts("N/A");
        }
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }

```
