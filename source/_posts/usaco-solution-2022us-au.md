---
title: "USACO 2022 US Open Contest Au 题解"
date: 2022-4-8 18:51:00
tags:
  - 贪心
  - Dp
categories:
  - USACO题解
---

[USACO - Au - T1](http://usaco.org/index.php?page=viewproblem2&cpid=1233)

考虑要么是奶牛去匹配苹果要么是苹果取匹配奶牛。

有一个很简单的贪心，能接住就接。

考虑对于 $x = x, y = t$ 建立笛卡尔坐标系。

对于一个苹果 $(a, b)$ 其能被奶牛接住当且仅当其在 $y = x + b - a$ 的直线下方。

这个东西本质上就是一个三角形，考虑将平面旋转 $(a, b) \to (\frac{a + b}{\sqrt 2}, \frac{a - b}{\sqrt 2})$。

这个 $\frac{1}{\sqrt 2}$ 其实本质上是没有关系的，放缩一下答案不会变化。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

namespace Legendgod {
    namespace Read {
//        #define Fread
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
    int t, x, n;
    int operator < (const Node &z) const { return t == z.t ? n < z.n : t < z.t; }
}ap[maxn], co[maxn];
int nap, nco;

multiset<Node> st;

signed main() {
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) {
        int q, t, x, cn;
        r1(q, t, x, cn);
        int nt = t + x, nx = t - x;
        if(q == 2) ap[++ nap] = { nt, nx, cn };
        else co[++ nco] = { nt, nx, cn };
    }
    sort(ap + 1, ap + nap + 1, [&](const Node& a, const Node& b) {
        return a.x < b.x;
    });
    sort(co + 1, co + nco + 1, [&](const Node& a, const Node& b) {
        return a.x < b.x;
    });
//    puts("APP");
//    for(i = 1; i <= nap; ++ i) printf("%d : (%d, %d, %d)\n", i, ap[i].t, ap[i].x, ap[i].n);
//    puts("COW");
//    for(j = 1; j <= nco; ++ j) printf("nj = %d : (%d, %d, %d)\n", j, co[j].t, co[j].x, co[j].n);
    int ans(0);
    for(j = 1, i = 1; i <= nap; ++ i) {
//        printf("%d : (%d, %d, %d)\n", i, ap[i].t, ap[i].x, ap[i].n);
        while(j <= nco && co[j].x <= ap[i].x) {
//            printf("nj = %d : (%d, %d, %d)\n", j, co[j].t, co[j].x, co[j].n);
            st.insert(co[j]), ++ j;
        }
        while(!st.empty() && ap[i].n > 0) {
            auto it = st.lower_bound((Node){ap[i].t + 1, 0, 0});
            if(it == st.begin()) break;
            -- it;
            int tmp = min(it->n, ap[i].n);
//            printf("i = %d\n", i);
            ans += tmp;
            ap[i].n -= tmp;
            Node tp = (*it);
            st.erase(it);
            if(tmp < tp.n) tp.n -= tmp, st.insert(tp);
        }
    }
    printf("%d\n", ans);
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }//
```

[USACO - Au - T2](http://usaco.org/index.php?page=viewproblem2&cpid=1234)

考虑如何判断两个表达式是不同的。

1. 实际有的变量相同。

2. 因为每个变量只会出现一次，对于每个变量出现的位置其后缀积相同。

我们单独考虑 $\times 0, \times 1$ 两种操作。

之后本质上就是一个像 $\tt Dp$ 的问题。

- $\times 1$ 事实上可以放到任意一个地方，但是贡献一样所以不用管。

- $\times 0$ 本质上就是消除之前的所有贡献。

考虑交错的部分是没有贡献的，如何处理交错的情况

只要保证不存在相邻两个操作是不同人而且是相同类型的就可以保证所有答案都是对的。

如果是相同类型我们只能出现一次，所以对于一边进行钦定即可。

考虑如果两边都进行钦定的话会发现相同的部分就不能放置了，所以考虑钦定一边满足条件，也就是放开一边的条件保证转移。 

不妨考虑放开 $j$ 转移的条件。

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

const int maxn = 2e3 + 5;
const int mod = 1e9 + 7;
int n, m;
char s[maxn], t[maxn];
char tmp[maxn];

void Solve(char *x,int &len) {
    int tot(0);
    for(int i = 1; i <= len; ++ i) {
        if(x[i] == '1') continue;
        if(x[i] == '0') tot = 0;
        tmp[++ tot] = (x[i] == '+' ? '+' : '2');
    }
    len = tot;
    for(int i = 1; i <= tot; ++ i) x[i] = tmp[i];
}

int f[maxn][maxn][2];

void Add(int &x,const int& c) { x = (x + 0ll + c) % mod; }

void Solve() {
    int i, j;
    r1(j);
    scanf("%s", s + 1), n = strlen(s + 1), Solve(s, n);
    scanf("%s", t + 1), m = strlen(t + 1), Solve(t, m);
//    for(i = 1; i <= n; ++ i) printf("%c", s[i]);
//    puts("");
//    for(i = 1; i <= m; ++ i) printf("%c", t[i]);
//    puts("");
    memset(f, 0, sizeof(f));
    if(!n || !m) return puts("1"), void();
    int ans(0);
    f[1][1][0] = 1;
    for(i = 1; i <= n + 1; ++ i) for(j = 1; j <= m + 1; ++ j) {
        for(int k = 0; k < 2; ++ k) {
            int V = f[i][j][k];
            if(!V) continue;
            if(i == n + 1 && j == m + 1) { Add(ans, f[i][j][k]); continue; }
            Add(f[i][j + 1][1], V);
            if(k == 0) Add(f[i + 1][j][0], V);
            else if(t[j - 1] != s[i]) Add(f[i + 1][j][0], V);
        }
    }
    printf("%d\n", ans);
}

signed main() {
	int i, j, T;
    r1(T); while(T --) Solve();
	return 0;
}
/*
1
3
0++
++9

*/

}


signed main() { return Legendgod::main(), 0; }//


```

[USACO - Au - T3](http://usaco.org/index.php?page=viewproblem2&cpid=1235)

考虑每条路径的答案，显然对于有连边关系的两个点，可以有 $\max(l_i - r_j)$。

考虑任意两个点，考虑从 $i \to 1 \to j$ 的路径，考虑设区间为 $[l_i, r_i], [l_j, r_j]$。

不妨假设左边区间小于右边，设 $1$ 节点权值为 $x$。可以得到左边的贡献是 $x - r_i$ 右边是 $l_j - x$ 考虑将 $x$ 消除掉，可以发现单边的贡献是 $\ge \frac{l_j - r_i}{2}$ 的。

所以 $B = 0$ 的情况就可以解决了。

> 注意是祖先关系，所以是可以继承的。

考虑 $B = 1$ 的时候要怎么做，我们之前猜测是可以达到下界的，对于单边的贡献我们是不用管的，或者说我们只需要考虑祖先链之间的贡献怎么样最小。

发现之前每个点到 $1$ 的贡献是 $\ge \frac{\max(l_j - r_i)}{2}$。考虑如何达到这个下界，对于这样的两个点我们肯定是取尽量靠近的，而对于其他的点是不会产生比他们两个大的贡献的，所以只需要考虑让每个 $a_i$ 尽量靠近 $\frac{\max(l_i) + \min(r_i)}{2}$。

---

或者看一看官方题解：

设 $w_i$ 表示我们之前取到的最靠近 $mid = \frac{\max(l_i) + \min(r_i)}{2}$ 的值。

我们有 $\forall i, |w_i - mid| \le \left\lceil \frac{\max(l_i) - min(r_i)}{2} \right\rceil$。

分类讨论 $\max(w_i, w_j) \le mid, \min(w_i, w_j) \ge mid$ 可以发现都是 $|w_i - w_j| \le \left\lceil \frac{\max(l_i) - min(r_i)}{2} \right\rceil$ 的。

对于 $w_i > mid, w_j < mid$ 的情况这个就对应了 $l_i - r_j$，这个我们也是可以保证 $\le ans$ 的。

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
int n, m, p[maxn], B;
typedef pair<int, int> pr;
pr a[maxn], b[maxn];
pr Gmax(pr A, pr B) {
    return {max(A.first, B.first), min(A.second, B.second)};
}

void Solve() {
    int i, j;
    r1(n);
    for(i = 2; i <= n; ++ i) r1(p[i]);
    for(i = 1; i <= n; ++ i) r1(a[i].first, a[i].second), b[i] = a[i];
    int ans(0); pr Mx = {0, 1e9};
    for(i = 1; i <= n; ++ i) {
        Mx = Gmax(Mx, a[i]);
        if(p[i]) {
            a[i].first = max(a[i].first, a[p[i]].first);
            a[i].second = min(a[i].second, a[p[i]].second);
        }
        ans = max(ans, a[i].first - a[i].second);
    }
    ans = max(ans, (Mx.first - Mx.second + 1) / 2);
    printf("%d\n", ans);
    if(B == 1) for(i = 1; i <= n; ++ i) printf("%d%c", max(b[i].first, min(b[i].second, (Mx.first + Mx.second) / 2)), " \n"[i == n]);
}

signed main() {
	int i, j, T;
    r1(T, B);
    while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```




