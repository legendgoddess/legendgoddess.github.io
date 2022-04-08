---
title:  "USACO 2022 US Open Contest Cu 题解"
date: 2022-4-8 8:13:00
tags:
  - 贪心
categories:
  - USACO题解
---

[USACO - Cu - T1](http://usaco.org/index.php?page=viewproblem2&cpid=1227)

发现翻转是改变一个区间的奇偶性，其声明只能对于偶数位置进行前缀区间翻转，所以考虑直接对于相邻两个数进行考虑。

翻转只会对于两个位置不同生效，考虑对于连续的一段一起翻转即可。

> 翻转贡献可能为 $1, 2$。看是否是一个前缀的形式。

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
int n, m;
char s[maxn];

signed main() {
	int i, j;
    r1(n);
    scanf("%s", s + 1);
    if(n & 1) -- n;
    int res(0), fl(0);
    for(i = n; i >= 1; i -= 2) {
        if(s[i] == s[i - 1]) { continue; }
        if(s[i - 1] == 'G') { if(!fl) ++ res, fl = 1; }
        else res += fl, fl = 0;
    }
    printf("%d\n", res);
	return 0;
}
/*
14
HGGHGHHGHHHGHG

2
*/

}


signed main() { return Legendgod::main(), 0; }//


```

[USACO - Cu - T2](http://usaco.org/index.php?page=viewproblem2&cpid=1228)

直接暴力枚举位置即可。

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
int n, m;
struct Node {
    int opt, x;
    int operator < (const Node &z) const { return x < z.x; }
}a[maxn];

char c[10];

signed main() {
	int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) {
        scanf("%s", c + 1), r1(a[i].x);
        if(c[1] == 'L') a[i].opt = 0;
        else a[i].opt = 1;
    }
    sort(a + 1, a + n + 1);
    int res(2e9);
    for(i = 1; i <= n; ++ i) {
        int x = a[i].x, sum(0);
        for(j = 1; j <= n; ++ j) {
            if(a[j].opt == 0) { if(x <= a[j].x) ; else ++ sum; }
            else { if(x >= a[j].x) ; else ++ sum;    }
        }
        res = min(res, sum);
    }
    printf("%d\n", res);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

[USACO - Cu - T3](http://usaco.org/index.php?page=viewproblem2&cpid=1229)

> 蠢题。

直接可以想到使用拓扑排序将标记下放，发现直接全部下放数太大了承受不住。

发现 $a_i$ 值域很小，可以考虑一个一个合成 $n$ 即可。

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
#define int long long
const int maxn = 1e6 + 5;
int n, m, a[maxn], f[maxn];
int head[maxn], cnt(1);
struct Edge {
    int to, next;
}edg[maxn << 1];
int deg[maxn];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
    ++ deg[v];
}

queue<int> q;
vector<int> dn;

signed main() {
	int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    r1(m);
    for(i = 1; i <= m; ++ i) {
        int L, c; r1(L, c);
        for(j = 1; j <= c; ++ j) {
            int x; r1(x); add(L, x);
        }
    }
    for(i = n - 1; i >= 1; -- i) if(deg[i] == 0) for(j = head[i];j;j = edg[j].next) {
        int to = edg[j].to; -- deg[to];
    }
    int ans(0);
    while(1) {
        int fl(1);
        for(i = 1; i <= n; ++ i) f[i] = 0;
        f[n] = 1;
        for(i = n; i >= 1; -- i) {
            if(a[i] >= f[i]) { a[i] -= f[i]; continue; }
            if(!head[i]) { fl = 0; break; }
            f[i] -= a[i], a[i] = 0;
            for(j = head[i];j;j = edg[j].next) f[edg[j].to] += f[i];
        }
        if(!fl) break;
        else ++ ans;
    }
    printf("%lld\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//



```




