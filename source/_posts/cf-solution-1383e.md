---
title: "CF1383E Strange Operation 题解"
date: 2022-3-26 18:25:00
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1383E - Codeforces](https://codeforces.com/problemset/problem/1383/E)

发现删除后得到的子串肯定是原串的一个子序列，考虑我们怎么样才能改变这个串。

考虑如何删除一个 $0$，也就是要么直接删掉要么前面为 $1$。那么本质上就是随意删除。

如果删除一个 $1$，我们需要保证前后存在一个是 $1$ 的位置

我们继续考虑最终的序列也就是在连续的一段 $1$ 中是可以任意删除的，如果是连续的一段 $0$ 我们是不能被 $1$ 影响的，也就是在区间内不存在任意的 $0$。

那么我们事实上只需要考虑限制，也就是我们把每个 $1$ 都拿出来考虑 $0$ 的限制。

考虑将 $S$ 变成一个数组 $\text{ \{pre(i)\} }$ 表示位置 $i - 1 \sim i$ 的 $0$ 的个数，可以发现 $\tt T$ 合法

当且仅当 $\tt T$ 的前缀和后缀的 $0$ 小于新的数组的第一个或者最后一个值，对于 $\tt T$ 是可以和 $S$ 进行匹配，也就是意味着对于 $\tt pre$ 存在一个子序列对应的元素大于等于 $\tt T$。

> 考虑我们做法是用来填 $1$ 的所以需要注意全部是 $0$ 的情况。

考虑对于填充 $1$ 的方案进行 $\tt Dp$。

考虑进行像序列自动机一样的 $\tt Dp$ 设 $f(i, j)$ 表示 $S$ 的位置为 $i$，$T$ 的位置为 $j$。

注意到一个串可能通过多种方式得到我们不妨钦定其是由最前面的位置进行得到的。

转移的时候考虑因为我们转移都是使用 $1$ 进行转移的，所以考虑从之前的一个位置 $k$ 的进行转移，也就是对于位置 $[k + 1, i - 1]$ 不存在 $pre_x$。然后更新的时候就是直接让其加上 $\tt dp_k$ 就行了。

我们考虑 $\sum pre_i = O(n)$ 所以我们维护前缀和暴力更新这个位置就行了。

当然我们需要压掉一维，发现填充的位置因为我们是通过钦定的所以是不会算重的，所以完全没必要记录。

复杂度是 $O(n)$ 的。

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
const int mod = 1e9 + 7;
int n, m;
char s[maxn];
int v[maxn], pos[maxn], sum[maxn];
long long dp[maxn];

signed main() {
	int i, j;
    scanf("%s", s + 1);
    n = strlen(s + 1);
    int fl(0);
    for(i = 1; i <= n; ++ i) (s[i] == '1') ? v[++ m] = fl, fl = 0 : ++ fl;
    v[++ m] = fl;
    if(m == 1) return printf("%d\n", n), 0;
    int ans = 1ll * (v[1] + 1) * (v[m] + 1) % mod;
//    printf("ans = %d\n", ans);
    dp[1] = sum[1] = 1;
    for(i = 2; i < m; ++ i) {
        for(j = 0; j <= v[i]; ++ j)
            dp[i] += sum[i - 1] - sum[pos[j] - 1];
        for(j = 0; j <= v[i]; ++ j) pos[j] = i;
        dp[i] %= mod;
        sum[i] = (sum[i - 1] + dp[i]) % mod;
    }
    ans = 1ll * ans * sum[m - 1] % mod;
    ans = (ans + mod) % mod;
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
