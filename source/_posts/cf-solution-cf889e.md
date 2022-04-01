---
title: "CF889E Mod Mod Mod 题解"
date: 2022-4-1 14:39:00
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 889E - Codeforces](https://codeforces.com/problemset/problem/889/E)

> 愚人节快乐。

首先考虑每个 $x$ 最终得到的序列肯定是单调不递增的。

那么对于最终的答案我们可以表示成 $x_n \times i + b$ 的形式。

我们考虑对于这个东西能不能进行 $\tt Dp$，设 $f(i, j)$ 表示考虑了前 $i$ 个数，最终的 $x$ 是 $j$ 得到的最大的 $b$。

我们会发现有很多无用状态，如果存在 $f(i, j - 1) \le f(i, j)$ 那么前面的部分肯定是没用了。

所以我们只需要考虑 $f(i, k)$ 随着 $k$ 增大值是递减的情况，我们转移的时候同时转移 $[0, k]$ 可以发现所有的情况都被转移过了。

对于 $a_{i + 1} > k$ 的情况，我们可以直接转移到 $f(i + 1, k)$。

不然的话我们肯定是考虑最后一段 $0, 1, 2, \cdots k \mod a_{i + 1}$ 的这一段进行转移，这样可以叠加尽量多的 $b$。

可以发现我们的状态 $k$ 每次是 $\div 2$ 或者不变的，所以状态数是 $\log$ 级别的。

> 不妨考虑 $x \equiv z \pmod y$，显然 $x = ay + z$，如果 $a > 1$ 我们尽量让 $z$ 最大。
> 
> 那么让 $z$ 取到 $y - 1$ 可以得到 $x = (a + 1)y - 1$ 所以 $y$ 取到 $\frac{x +1}{2}$。显然 $x < y$ 那么 $x$ 每次是除以 $2$ 的，可以发现状态数是 $\log$ 级别的。

$$
\begin{aligned}
f(i + 1, j) &= f(i, j), a_{i + 1} > j \\\\
f(i + 1, a_{i + 1} - 1) &= f(i, j) + \left \lfloor \frac{j - (a_{i + 1} - 1)}{a_{i + 1}} \right \rfloor i \times a_{i + 1} \\\\
f(i + 1, j \mod a_{i + 1}) &= f(i, j) + (j - j \mod a_{i + 1}) \times i \\\\
\end{aligned}
$$

> $\color{red}\text{pay attention ! }$注意这里取下整最好优先计算，不然如果乘上了别的东西可能会造成影响。

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
typedef long long int64;
int n, m;
int64 a[maxn], buc[maxn];
int btot(0);

signed main() {
	int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);

    static map<int64, int64, greater<int64> > mp;
    mp[a[1] - 1] = 0;
    function<void(int64&, const int64& c)> Max;
    Max = [&](int64 &x, const int64& c) { c > x ? x = c : 0; return ; };
    for(i = 1; i < n; ++ i) {
        btot = 0;
        for(auto it = mp.begin(); it != mp.end(); ++ it) {
            if(it -> first < a[i + 1]) break;
            Max(mp[a[i + 1] - 1], it -> second + (it -> first - a[i + 1] + 1) / a[i + 1] * i * a[i + 1]);
            Max(mp[it -> first % a[i + 1]], it -> second + (it -> first - it -> first % a[i + 1]) * i);
            buc[++ btot] = it -> first;
        }
        for(j = 1; j <= btot; ++ j) mp.erase(buc[j]);
    }

    int64 ans(0);
    for(auto it = mp.begin(); it != mp.end(); ++ it)
        ans = max(ans, n * (it -> first) + (it -> second));
    printf("%lld\n", ans);
	return 0;
}
/*
10
100 68 92 92 62 74 50 54 85 99
*/
}


signed main() { return Legendgod::main(), 0; }//


```


