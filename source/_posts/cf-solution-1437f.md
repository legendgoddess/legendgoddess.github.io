---
title: "CF1437F Emotional Fishermen 题解"
date: 2022-3-30 14:23:00
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1437F - Codeforces](https://codeforces.com/problemset/problem/1437/F)

> 题目比较弱智，但是我更加弱智。

首先考虑排序之后肯定是没有关系的，从小到大进行插入。

考虑插入了 $i$ 位置之后的结果 $f_i$，我们发现如果需要插入必须找到一个位置 $j$ 使得 $2a_j \le a_i$，当然对于 $\forall k \le j$ 都是可以进行转移的。

我们考虑位置 $j$ 前面满足 $2a_k \le a_j$ 的最大位置 $x_j$，那么经过 $j$ 的插入之后总共有 $x_j + 1$ 个元素。同理对于 $i$ 来说序列的长度是固定的就是 $x_i +1$。

考虑插入到了位置 $j$，也就是通过 $f_j$ 进行转移，对于只受 $i$ 影响的数我们肯定可以在 $i$ 之后任意排列，数的个数总共有 $x_i - x_j - 1$。

> $j$ 也是需要算进去的。

对于位置 $i$ 可以考虑还有几个位置可以放置，显然有 $n - 1 - 1 - x_j$。

> 减去 $i, j, x_j$。

所以方案数就是 $A_{n - 2 - x_j}^{x_i - x_j - 1} = \dfrac{(n - 2 - x_j)!}{(n - 1 - x_i)!}$。

直接前缀和优化即可。

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
const int mod = 998244353;
int n, m, a[maxn], fac[maxn], finv[maxn];
int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

void init(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
}

int f[maxn], sum[maxn];

signed main() {
	int i, j;
    init(maxn - 5);
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    sort(a + 1, a + n + 1);
    if(a[n - 1] * 2 > a[n]) return puts("0"), 0;
    f[0] = 1, sum[0] = fac[n - 1];
    int mx(0);
    for(i = 1; i <= n; ++ i) {
        while(mx + 1 < i && 2 * a[mx + 1] <= a[i]) ++ mx;
//        printf("mx = %d\n", mx);
        f[i] = 1ll * sum[mx] * finv[n - mx - 1] % mod;
        sum[i] = (sum[i - 1] + 1ll * f[i] * fac[n - mx - 2] % mod) % mod;
//        printf("%d : %d\n", i, f[i]);
    }
    printf("%d\n", f[n]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
