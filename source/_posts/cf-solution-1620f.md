---
title: "CF1620F Bipartite Array 题解"
date: 2022-4-5 19:45:00
categories:
  - CF题解
---

[Problem - 1620F - Codeforces](https://codeforces.com/problemset/problem/1620/F)

二分图本质上就是没有奇环，注意给定的是一个排列。

考虑什么时候会出现奇环，考虑如果 $(i, j), (j, k)$ 那么必然存在 $(i, k)$ 这样就去世了。

也就是如果存在 $(i, j)$ 那么 $\forall k \in [j + 1, n], a_k \ge a_j$。

---

> 我菜了。

既然结论已经想到这个份上了，直接考虑 $\tt Dp$。

我们注意结论：序列中不存在 $i < j < k, p_i > p_j > p_k$。

考虑进行填数设 $f(i, x, y)$ 表示填了 $i$ 个数，最大值为 $x$，已经填好的逆序对末尾的最大值为 $y$，能否没有奇环。

考虑优化状态，对于两个序列 $p_1, p_2$ 在相同的 $x$ 下选取较小的 $y$ 更加有潜力。

显然对于 $(i, y)$ 是固定的，$x$ 越小序列越容易合法。

考虑将状态变成 $f(i, x)$ 表示能取到的最小的 $y$。

> 如果不合法就是 $+\infty$。

转移方程，设 $z = \pm p_{i + 1}, f(i, x) = y(z \ge y)$。

- $f(i + 1, x) = z, z < x$。

- $f(i + 1, z) = y, z \ge x$。 

发现其中至少有一个值是 $z$，所以状态是：

$f(i, 0/1, 0/1)$ 表示当前考虑到第 $i$ 个位置，当前数是 $+/-$，$x/y$ 是当前位置，值存的是剩下的那个数，复杂度是 $O(n)$ 的。

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

const int maxn = 1e6 + 5;
const int inf = 2e9;
int n, m, p[maxn], f[maxn][2][2];

struct Node {
    int i, j, k;
}pre[maxn][2][2];
#define rep(i, a, b) for (int i = (a); i <= (b); i++)
#define per(i, a, b) for (int i = (a); i >= (b); i--)
void Solve() {
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(p[i]);
    for(i = 0; i <= n + 1; ++ i) for(j = 0; j < 2; ++ j) for(int k = 0; k < 2; ++ k) f[i][j][k] = inf;
    f[1][1][0] = f[1][0][0] = - inf;
    for(i = 1; i < n; ++ i) for(j = 0; j < 2; ++ j) for(int k = 0; k < 2; ++ k) {
        Node tmp = {i, j, k};
        int x, y;
        if(!j && !k) x = p[i], y = f[i][j][k];
        else if(!j && k) x = f[i][j][k], y = p[i];
        else if(j && !k) x = - p[i], y = f[i][j][k];
        else if(j && k) x = f[i][j][k], y = - p[i];
        for(int z = 0; z < 2; ++ z) {
            int np = p[i + 1] * (z == 0 ? 1 : -1);
            if(np < y) continue;
            if(np < x) if(f[i + 1][z][1] > x) f[i + 1][z][1] = x, pre[i + 1][z][1] = tmp;
            if(np >= x) if(f[i + 1][z][0] > y) f[i + 1][z][0] = y, pre[i + 1][z][0] = tmp;
        }
    }
    for(i = 0; i < 2; ++ i) for(j = 0; j < 2; ++ j) if(f[n][i][j] < inf) {
        puts("YES");
        int ni = i, nj = j, dep = n;
        static vector<int> vc; vc.clear();
        while(dep > 0) {
            assert(f[dep][ni][nj] < inf);
            vc.push_back((ni == 0 ? 1 : -1) * p[dep]);
            Node tmp = pre[dep][ni][nj];
            dep = tmp.i, ni = tmp.j, nj = tmp.k;
        }
        reverse(vc.begin(), vc.end());
        for(const int& v: vc) printf("%d ", v);
        puts("");
        return ;
    }
    puts("NO");
}
/*
1
6
1 3 2 6 5 4
*/
signed main() {
	int i, j, T;
    r1(T); while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


