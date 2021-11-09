---
title: 佳佳的 Fibonacci
date: 2021-09-23 15:41:08
tags: 
  - 数论
  - Dp
categories: 
  - LOJ题解
---


<h3><center>LOJ #10222. 「一本通 6.5 例 4」佳佳的 Fibonacci</center></h3>

> [#10222. 「一本通 6.5 例 4」佳佳的 Fibonacci](https://loj.ac/p/10222)

首先直接对于这个题目不好入手，我们不妨看看题目中 $S(n)$ 是否有递推式，因为 $T(n) = \sum_{i = 1} ^ n S(n) - S(i - 1)$。

> 这个说实话挺显然的。

首先会想到 $S(n) - S(n - 1) = F(n)$ 但是这个没有什么作用，不过考虑 $F(n) = F(n- 1) + F(n - 2)$ 那么这个 $S(n)$ 是否也可以拆成这样类似的形式。

考虑 $S(n) = S(n - 1) + F(n)$ 之后我们考虑 $F(n)$ 能否拆分成若干个数的和，显然是可以的 $F(n) = F(n - 1) + F(n - 2)$，$F(n - 1) = F(n - 2) + F(n - 3)$

那么最终的一项肯定是

$F(3) = F(2) + F(1)$ 其中多了一个 $F(2)$。

那么我们的狮子也是显然可以推出来了：

$F(n) = S(n - 2) + 1$。

将这个柿子带入之前的公式得到：
$$
\begin{aligned}
T(n) =& \sum_{i = 1} ^ n S(n) - S(i - 1) \\
=&nS(n) - \sum_{i = 1} ^ {n - 1} S(n) \\
=&n(F(n + 2) - 1) - \sum_{i = 3} ^ {n + 1} F(i) - 1 \\
=&F(n + 2) - n - (S(n + 1) - S(2)) + n - 1\\
=&F(n + 2) - 1 - (F(n + 3) - 2 - 1) \\
=&F(n + 2) - F(n + 3) + 2
\end{aligned}
$$
我们直接放一手矩阵加速即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
//#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
#define getchar gc
#endif // Fread

template <typename T>
void r1(T &x) {
	x = 0;
	char c(getchar());
	int f(1);
	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
	x *= f;
}

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}
//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;
typedef long long ll;
ll n, m;

struct Matrix {
    int a[2][2];
    Matrix(void) { memset(a, 0, sizeof(a)); }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < 2; ++ i) {
            for(int j = 0; j < 2; ++ j) {
                for(int k = 0; k < 2; ++ k) {
                    res.a[i][j] = (res.a[i][j] + 1ll * a[i][k] * z.a[k][j] % m) % m;
                }
            }
        }
        return res;
    }
}F, tmpf;
void ksm(Matrix &x,Matrix tmp,ll mi) {
    while(mi) {
        if(mi & 1) x = x * tmp;
        mi >>= 1;
        tmp = tmp * tmp;
    }
}

int Solve(ll x) {
    F.a[0][0] = F.a[0][1] = 1;
    ksm(F, tmpf, x - 2);
    return F.a[0][1];
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    tmpf.a[1][0] = 1;
    tmpf.a[0][1] = tmpf.a[1][1] = 1;
    int ans = 1ll * n * Solve(n + 2) % m - Solve(n + 3) + 2;
    ans = (ans + m) % m;
    printf("%d\n", ans);
	return 0;
}

```



