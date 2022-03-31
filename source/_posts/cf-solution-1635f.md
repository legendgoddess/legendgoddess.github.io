---
title: "CF1635F Closest Pair 题解"
date: 2022-3-31 9:27:00
tags:
  - 线段树
categories:
  - CF题解
---

[Problem - 1635F - Codeforces](https://codeforces.com/problemset/problem/1635/F)

首先考虑一下如果已知一个位置 $i$ 怎么样选择 $j$ 最优，发现这个东西不是很好维护。

但是我们发现不知道怎样选择我们可以考虑有哪些 $j$ 可能成为最优解。

如果说 $x_{j1} < x_{j2} \ \cap\ w_{j1} \le w_{j2} $，那么 $j2$ 是没有必要的。

同理如果考虑已知 $j$，对于 $x_{i1} < x_{i2} \ \cap \ w_{i1} \ge w_{i2}$ 那么 $i1$ 是没有必要的。

显然这个东西是有单调性的，我们考虑维护一个单调栈。

不妨考虑从左向右扫，之后考虑加入一个元素肯定是要计算这个元素与之前元素的贡献。

考虑加入一个元素的时候一直进行更新直到找到一个没有必要更新的位置。

使用单调栈维护**不合法**的递增二元组即可。

这样我们加入的时候可以直接使用和当前位置不能有单调性的元素。

询问离线即可。

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

const int maxn = 3e5 + 5;
#define int long long
int n, m;

struct Node {
    int x, y;
    int operator < (const Node &z) const {
        return x < z.x && y < z.y;
    }
}a[maxn];

const int inf = 8e18;
vector<pair<int, int> > q[maxn];
int ans[maxn];
int st[maxn], ed(0);

int t[maxn << 2], md[maxn << 2];
struct Seg {
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid md[p]
    void build(int p,int l,int r) {
        t[p] = inf;
        if(l == r) return ;
        mid = (l + r) >> 1;
        build(ls, l, mid), build(rs, mid + 1, r);
    }
    void Insert(int p,int l,int r,int pos,int val) {
        t[p] = min(t[p], val);
        if(l == r) return ;
        if(pos <= mid) Insert(ls, l, mid, pos, val);
        else Insert(rs, mid + 1, r, pos, val);
    }
    int Ask(int p,int l,int r,int ll,int rr) {
        if(ll <= l && r <= rr) return t[p];
        int res(inf);
        if(ll <= mid) res = min(res, Ask(ls, l, mid, ll, rr));
        if(mid < rr) res = min(res, Ask(rs, mid + 1, r, ll, rr));
        return res;
    }
    #undef ls
    #undef rs
    #undef mid
}T;

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) {
        r1(a[i].x, a[i].y);
    }
    for(i = 1; i <= m; ++ i) {
        int l, r;
        r1(l, r), q[r].push_back({l, i});
    }
    T.build(1, 1, n);
    for(i = 1; i <= n; ++ i) {
        while(ed > 0) {
            Node tmp = a[st[ed]];
            T.Insert(1, 1, n, st[ed], (a[i].x - tmp.x) * (a[i].y + tmp.y));
            if(tmp < a[i]) break;
            else -- ed;
        }
        st[++ ed] = i;
        for(const auto &v : q[i]) ans[v.second] = T.Ask(1, 1, n, v.first, i);
    }
    for(i = 1; i <= m; ++ i) printf("%lld\n", ans[i]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


