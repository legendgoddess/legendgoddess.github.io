---
title: "CF1615F LEGOndary Grandmaster 题解"
date: 2022-4-4 20:14:00
categories:
  - CF题解
---

[Problem - 1615F - Codeforces](https://codeforces.com/problemset/problem/1615/F)

首先怀疑一手这个东西是 $\tt Dp$。

> 就只会怀疑了 $\cdots$

有一个很强的转化，考虑先对于每个串的偶数位置取反，之后原来的操作本质上就是交换两个数。

考虑对于位置 $a_i, a_{i + 1}$ 经过操作后是 $!a_i, !a_{i + 1}$。

经过变换后是 $a_i, !a_{i + 1}$ 和 $!a_{i}, a_{i + 1}$。发现对于 $a_i \ne a_{i + 1}$ 经过交换是不变的，所以这个变换满足充要条件。

考虑合法的条件就是两个序列 $1$ 的个数相同，考虑如何计算最小的操作次数。

题目说只能 $s$ 动所以比较简单考虑通过交换去填充 $1$ 的位置，显然每个 $1$ 填充的最优位置是确定的。

不妨考虑 $S$ 中第 $i$ 个 $1$ 位置在 $x_i$，$T$ 中为 $y_i$，答案就是 $\sum_{i \ge 1} |x_i - y_i|$。

还要继续转化，考虑  $S$ 中 $1 \sim i$ 的 $1$ 的个数为 $a_i$，$T$ 中为 $b_i$。

答案同样可以表示为 $\sum_{i = 1} ^ n |a_i - b_i|$，考虑我们最终肯定是所有 $a_i = b_i$，考虑每次移动恰好使得一个 $|a_i - b_i|$ 减少 $1$，那么这个就是答案。

直接对于整个字符串裂成两半计算即可。

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

const int maxn = 2e3 + 5;
const int mod = 1e9 + 7;
int n, m, pre[maxn][maxn << 1], suf[maxn][maxn << 1];
char s[maxn], t[maxn];
const int N = 2e3 + 5;

void Add(int& x,const int& c) { x = (x + c) % mod; }
int pd(char *s, int id,int y) {
    if(s[id] == '?') return true;
    if(s[id] - '0' == y) return true;
    return false;
}

void Solve() {
    int i, j;
    r1(n);
    scanf("%s", s + 1);
    scanf("%s", t + 1);
    for(i = 0; i <= n + 1; ++ i) for(j = - n - 1; j <= n + 1; ++ j) suf[i][j + N] = pre[i][j + N] = 0;
    pre[0][N] = suf[n + 1][N] = 1;
    for(i = 0; i < n; ++ i) for(j = - n; j <= n; ++ j) if(pre[i][j + N]) {
        for(int dx = 0; dx <= 1; ++ dx) for(int dy = 0; dy <= 1; ++ dy) if(pd(s, i + 1, dx ^ (i & 1)) && pd(t, i + 1, dy ^ (i & 1))) {
            Add(pre[i + 1][N + j + dx - dy], pre[i][j + N]);
        }
    }
    for(i = n + 1; i > 1; -- i) for(j = - n; j <= n; ++ j) if(suf[i][j + N]) {
        for(int dx = 0; dx <= 1; ++ dx) for(int dy = 0; dy <= 1; ++ dy) if(pd(s, i - 1, dx ^ (i & 1)) && pd(t, i - 1, dy ^ (i & 1))) {
            Add(suf[i - 1][N + j + dx - dy], suf[i][j + N]);
        }
    }
    int ans(0);
    for(i = 1; i <= n; ++ i) for(j = - n; j <= n; ++ j)
        Add(ans, 1ll * pre[i][j + N] * suf[i + 1][- j + N] % mod * abs(j) % mod);
    printf("%d\n", ans);
}

signed main() {
	int i, j, T;
    r1(T); while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


