---
title: CF1603D Artistic Partition 题解
date: 2022-3-12 20:30:20
tags:
  - 反演
  - Dp
categories:
  - CF题解
---

[Problem - D - Codeforces](https://codeforces.com/contest/1603/problem/D)

> 好玩题。

首先考虑如果我们已经知道了 $n$ 的划分这个题应该怎么做。

我们可以考虑枚举每一段的 $\tt gcd$ 不妨设为 $d$，之后考虑进行计算。

$$
\begin{aligned}
& \sum_{d = L} ^ {R} \sum_{k = 1} ^ {\lfloor \frac{R}{d} \rfloor} \mu(k) \times\binom{\lfloor \frac{R}{kd} \rfloor}{2} \\\\ 
\end{aligned}
$$

也就是考虑枚举每一个公约数，之后考虑两个数的公约数是当前的 $\tt gcd$ 的贡献。

> 当然还要加上每个数自身的贡献。

我们既然要考虑其最小值，我们考虑上面这个式子能取到最小值的是尽量让其 $= 0$。

考虑对于一个 $[L, R]$ 只要让后面的组合数等于 $0$ 即可，我们考虑取 $d = L$ 保证限制进行考虑，可以得到 $1 \le k$，那么只需要让 $k = 1, \lfloor \frac{R}{L} \rfloor = 0$ 即可。

显然我们的 $R$ 最大可以取到 $2L - 1$，那么可以得到一个有趣的结论。

- 我们考虑每次进行划分，区间肯定是 $\times 2$，那么如果 $2 ^ k \ge n$ 显然答案就是 $n$ 了。

但是很显然，这个东西甚至需要枚举划分数，直接去世。

那么对于划分数我们还有一种考虑，就是进行 $\tt Dp$，方程比较显然。

$$
\begin{aligned}
f(i, j) = \min \{ f(i - 1, k) + c(k + 1, j) \}
\end{aligned}
$$

乍一看状态是 $O(n ^ 2)$ 的，复杂度更高。别忘记了我们上面的边界那么我们可以得到 $k$ 是 $O(\log n)$ 级别的。

但是转移复杂度还是 $O(n)$ 呀？

我们考虑将 $c$ 的式子拿出来看看。

$$
\begin{aligned}
c(L, R) &= \sum_{i = L} ^ R \sum_{j = i} ^ R [ \gcd(i, j) \ge L ] \\\\
&= \sum_{d = L} ^ R \sum_{i = L} ^ R \sum_{j = i} ^ R [ \gcd(i, j) == d ] \\\\
&= \sum_{d = L} ^ R \sum_{i = 1} ^ {\lfloor \frac{R}{d} \rfloor } \sum_{j = i} ^ {\lfloor \frac{R}{d} \rfloor } [ \gcd(i, j) == 1 ]\\\\
&= \sum_{d = L} ^ R \sum_{j = 1} ^ {\lfloor \frac{R}{d} \rfloor } \sum_{i = 1} ^ j [ \gcd(i, j) == 1 ]\\\\
&= \sum_{d = L} ^ R \sum_{j = 1} ^ {\lfloor \frac{R}{d} \rfloor } \varphi(j) \\\\
&= \sum_{d = L} ^ R sum\varphi ( \lfloor \frac{R}{d} \rfloor )
\end{aligned}
$$

发现这个东西可以进行整除分块，那么算 $c$ 的复杂度是可以接受的，但是这个东西不能单调队列和斜率优化呀？

我们尝试一下其决策是不是单调的，也就是带入四边形不等式。

$$
\begin{aligned}
c(l, r) + c(l + 1, r + 1) \le  c(l + 1, r) + c(l, r + 1) \\\\
c(l, r) - c(l + 1, r) \le c(l, r + 1) - c(l + 1, r + 1)
\end{aligned}
$$

显然成立，我们可以进行决策单调性优化。

> 笔者采用分治的形式，比较好些。

代码的时候注意边界的细节，特别是预处理 $\tt Dp$ 的时候需要考虑转移区间 $[L, R]$ 与 $\tt mid$ 的关系。

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

const int maxn = 1e5 + 5;
constexpr int N = 17;
constexpr int M = 1e5;
#define int long long
int n, K;
int f[maxn][N + 3];
int vis[maxn], pr[maxn], phi[maxn], sp[maxn], tot(0);

void init(int x) {
    phi[1] = 1;
    for(int i = 2; i <= x; ++ i) {
        if(!vis[i]) pr[++ tot] = i, phi[i] = i - 1;
        for(int j = 1, k; k = i * pr[j], k <= x && j <= tot; ++ j) {
            vis[k] = 1;
            if(!(i % pr[j])) {
                phi[k] = phi[i] * pr[j];
                break;
            }
            phi[k] = phi[i] * phi[pr[j]];
        }
    }
//    for(int i = 1; i <= 20; ++ i) printf("%d : %d\n", i, phi[i]);
    for(int i = 1; i <= x; ++ i) sp[i] = sp[i - 1] + phi[i];
}


int C(int l,int r) {
    if(l > r) return 0;
    int res(0);
    for(int i = l, last; i <= r; i = last + 1) {
        last = min(r, r / (r / i));
        res += (last - i + 1) * sp[r / i];
    }
    return res;
}

void Gdp(int l,int r,int L,int R) {
    if(l > r) return ;
//    printf("(%d %d)\n", l, r);
    int mid = (l + r) >> 1;
    int sum(C(R + 1, mid)), ps(0), mn(0x3f3f3f3f);
//    printf("K = %lld\n", K);
    for(int i = min(mid, R); i >= L; -- i) {
        sum += sp[mid / i];
        int tmp = sum + f[i - 1][K - 1];
        if(tmp < mn) {
            mn = tmp, ps = i;
        }
    }
    f[mid][K] = mn;
    Gdp(l, mid - 1, L, ps), Gdp(mid + 1, r, ps, R);
}

void Solve() {
    r1(n, K);
    int x = n, tmp = 0;
    while(x) ++ tmp, x >>= 1;
//    printf("tmp = %d\n", tmp);
    if(tmp <= K) return printf("%lld\n", n), void();
    printf("%lld\n", f[n][K]);
}

signed main() {
	int i, j, T(1);
	init(maxn - 5);
    for(i = 1; i <= M; ++ i) f[i][1] = C(1, i);
//    printf("s = %lld\n", C(1, 3));
    for(K = 2; K <= N; ++ K) Gdp(1, M, 1, M);
    r1(T);
    while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }

```


