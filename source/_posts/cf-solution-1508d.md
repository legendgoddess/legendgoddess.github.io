---
title: "CF1508D Swap Pass 题解"
date: 2022-4-6 18:20:00
tags:
  - 置换
  - 图论
categories:
  - CF题解
---

[Problem - 1508D - Codeforces](https://codeforces.com/problemset/problem/1508/D)

首先将 $a_i$ 放到排列上去，考虑对于 $a_i \to i$ 进行连边，本质上又是置换的情况。

考虑对于置换进行操作，对于同一个置换环进行一次交换就可以将环归位而且不会相交。

考虑对于 $(u, v)$ 在不同的环中，我们使用交换本质上就是将两个环合并到一起，但是如何保证不会出现相交的情况。

最终肯定是只有 $1$ 个环，然后来进行操作。

对于凸包的情况考虑随便选择一个点，之后其他点和当前点连线肯定是不相交的，所以我们合并环的操作只能使用相邻两个点。显然我们交换相邻两个点是肯定可以使其变成一个环的。

如果不是凸包的话，发现随便画个图就去世了。

> 如果发现不对的情况，可以先考虑什么情况是不对的，看看能不能进行处理。

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
int n, m, fa[maxn], pos[maxn];
int getfa(int x) { return x == fa[x] ? x : fa[x] = getfa(fa[x]); }
int pd(int x,int y) { return getfa(x) != getfa(y); }
void merge(int u,int v) {
    u = getfa(u), v = getfa(v);
    if(u != v) fa[u] = v;
}

struct Node {
    int x, y, a, id;
    double sl;
    int operator < (const Node& z) const {
        return sl < z.sl;
    }
}a[maxn];

signed main() {
	int i;
    r1(n); Node tmp;
    for(i = 1; i <= n; ++ i) {
        r1(tmp.x, tmp.y, tmp.a), tmp.id = i;
        if(tmp.a != i) a[++ m] = tmp;
    }
    for(i = 1; i <= n; ++ i) fa[i] = i;
    n = m;
    if(!m) return puts("0"), 0;
    tmp = a[1]; int ps = 1;
    for(i = 2; i <= n; ++ i)
        if((a[i].x < tmp.x) || (a[i].x == tmp.x && a[i].y < tmp.y)) tmp = a[i], ps = i;
    if(tmp.id != 1) swap(a[1], a[ps]);
    tmp = a[1];
    for(i = 2; i <= n; ++ i) a[i].sl = atan2(a[i].y - tmp.y, a[i].x - tmp.x);
    sort(a + 2, a + n + 1);
    for(i = 1; i <= n; ++ i) {
        int u = a[i].id, v = a[i].a;
        if(pd(u, v)) merge(a[i].id, a[i].a);
    }
    vector<pair<int, int> > ans; ans.clear();
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, getfa(i));
    for(i = 2; i < n; ++ i) {
        int u = a[i].id, v = a[i + 1].id;
        if(pd(u, v)) {
            ans.push_back({a[i].id, a[i + 1].id});
            merge(a[i].id, a[i + 1].id);
            swap(a[i].a, a[i + 1].a);
        }
    }
    for(i = 1; i <= n; ++ i) pos[a[i].id] = i;
    while(a[1].id != a[1].a) {
        int u = pos[a[1].a];
        ans.push_back({a[u].id, a[1].id});
        swap(a[u].a, a[1].a);
    }
    printf("%llu\n", ans.size());
    for(const auto& v: ans) printf("%d %d\n", v.first, v.second);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```
