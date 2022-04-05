---
title: "CF1225G To Make 1 题解"
date: 2022-4-5 10:28:00
tags:
  - 数论
  - 贪心
categories:
  - CF题解
---

[Problem - 1225G - Codeforces](https://codeforces.com/problemset/problem/1225/G)

### 一点想法

首先我们看这个神奇的函数 $f$。

如果说 $x = k^a\times b$ 的话，那么 $f(x) = b$。

我们考虑对于 $x = k^{a_1}b_1, y = k^{a_2}b_2, a_1 < a_2$。

我们可以得到 $(x + y) = k^{a_1}(k^{a_2-a_1}b_2 + b_1)$，显然右边部分肯定不是 $k$ 的倍数，是可以直接算出来的。

考虑题目中的限制是最终的答案为 $1$，那么意味着最后一次合并有 $k | x +y$ 。

注意到 $n = 16, k = 2000, \sum a_i \le 2000$，是不是可以进行状压。

复杂度貌似是 $O(2^n\sum a_i)$。考虑设 $f(S, i)$ 表示集合 $S$ 中的数能否拼出 $i$。

转移的时候枚举一下加入哪个数，复杂度是 $O(2^nn\sum a_i)$。是不是可以 $\tt bitset$。

考虑枚举转移位置和 $S$，我们貌似可以预处理出来转移矩阵，复杂度是 $\frac{n^2}{64} = 4$ 貌似优秀一点，就可以过了。

> 貌似对了。看看实现更加优秀的正解。

---

### $\tt solution$

考虑 $\tt Dp$ 的本质，就是考虑对于若干个 $a_i$ 进行 $\div k^b_i$ 次操作，使得最终 $\sum_{i = 1} ^ n a_i \times k^{-b_i} = 1$。

考虑证明充分性：

如果有 $b$ 序列是可以推出合法解的，首先序列长为  $1, 2$ 是正确的，考虑对于 $n > 2$ 的时候，设 `tmp = max(b[i])`，如果两个 `tmp` 可以合并被 $k$ 整除，那么直接归纳。不然继续合并，因为分母是 $k^{tmp}$ 最终答案是 $1$ 所以肯定是可以合并下去的，所以归纳成立。

那么我们现在是需要对于序列 $b$ 进行决策，根据我们证明我们是通过 $b_i$ 逐渐变小进行归纳的，所以需要对于 $b_i$ 从大到小进行计算。

设 $f(S, i)$ 表示集合 $S$ 最终能否是 $i$。

- 加入一个 $b_i = 0$，转移到 $f(S, i) \leftarrow f(nS, i - a_i)$。

- 进行 $\div K$ 操作，$f(S, i) \leftarrow f(S, ik)$。

后面这个使用完全背包即可，用 $\tt bitset$ 优化。

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
int n, K, sum(0), a[maxn], b[maxn];
bitset<maxn> f[(1 << 16) + 5];
priority_queue<pair<int, int>> q;


void dfs(int S,int c) {
    if(!S) return ;
    if(c * K <= sum && f[S][c * K]) {
        for(int i = 1; i <= n; ++ i) if((S >> (i - 1)) & 1) ++ b[i];
        dfs(S, c * K);
        return ;
    }
    for(int i = 1; i <= n; ++ i) if((S >> (i - 1)) & 1) {
        int nS = (S ^ (1 << (i - 1)));
        if(a[i] <= c && f[nS][c - a[i]]) {
            dfs(nS, c - a[i]);
            return ;
        }
    }
}

signed main() {
	int i, j;
    r1(n, K);
    for(i = 1; i <= n; ++ i) r1(a[i]), sum += a[i];
    f[0][0] = 1;
    for(int S = 1; S < (1 << n); ++ S) {
        for(j = 1; j <= n; ++ j) if((S >> (j - 1)) & 1) f[S] |= f[S ^ (1 << (j - 1))] << a[j];
        for(j = sum / K; j >= 1; -- j)
            f[S][j] = f[S][j] | f[S][j * K];
    }
    int z = (1 << n) - 1;
    if(f[z][1] == 0) return puts("NO"), 0;
    puts("YES");
    dfs(z, 1);
    for(i = 1; i <= n; ++ i) q.push({b[i], a[i]});
    while(q.size() > 1) {
        auto u = q.top(); q.pop();
        auto v = q.top(); q.pop();
        printf("%d %d\n", u.second, v.second);
        int x = u.second + v.second, y = u.first;
        while(y && !(x % K)) x /= K, -- y;
        q.push({y, x});
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
