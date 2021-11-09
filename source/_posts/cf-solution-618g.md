---
title: CF618G Combining Slimes
date: 2021-09-25 10:26:20
tags: 
  - 期望
  - Dp
categories: 
  - CF题解
---

<h3><center>CF618G Combining Slimes</center></h3>

> [CF618G Combining Slimes](https://codeforces.ml/problemset/problem/618/G)

首先考虑根据期望的线性性质对于每一个数分开来计算贡献，之后再求出每一个数出现的概率即可。

> 也不是很清楚这个东西是不是线性性质。
>
> 但是说实话就是对于所有数一起考虑是不能入手的。

之后我们发现事实上任意的数都有可能出现，发现其没有取模，我们不妨计算一下一个数可能出现的概率。

如果说这个数是 $x$，那么其概率是 $(1 - 10^{-9})^{2^{x - 1}}$。算一下发现 $x = 50$ 的时候这个东西基本上都趋近于 $0$ 了。那么我们可以直接只考虑前面的 $50$ 个数。

我们考虑算每个数至少出现一次的概率，那么设其为 $a(i, j)$ 那么可以得到转移方程：
$$
a(i, j) = 
a(i - 1, j - 1) \times a(i, j - 1)
$$

> 就是考虑前面出现的同时出现两个数的情况，那么肯定有一个拼成的数需要占用一个位置。

显然初值是 $a(1, 1) = p, a(1, 2) = 1 - p$。

但是发现 $n$ 实际上还是很大，我们不能将所有的值都求出来，根据上文的经验，我们发现当 $a(i, j)$ 其中 $i > 50$ 的时候也趋近于零，那么之后的所有值我们都可以用 $a(50, j)$ 进行代替。

之后我们计算转移的方程 $f(i, j)$ 表示考虑了最后的 $i$ 个格子，从右开始数第 $i$ 个格子恰好是 $j$ 后面的值的期望。

注意因为是恰好，我们之前计算的 $a$ 是至少，那么我们不妨设 $A(i, j) = a(i, j) \times (1 - a(i - 1, j))$ 也就是恰好。

> 就是说之前都不是 $j$ 然后正好当前位置是 $j$ 的概率。

那么可以得到 ：
$$
\begin{aligned}
f(i, j) = j + \frac{\sum_{k = 1} ^ {j - 1} f(i - 1, k) \times A(i - 1, k)}{\sum_{k = 1} ^ {j - 1} A(i - 1, k)}, j > 1
\end{aligned}
$$
我们发现 $j = 1$ 的时候没有计算进去，我们考虑如果第一个数是 $1$ 而且后面都没有与其合并，那么下一个数必然就是 $2$ 。

> 这里指先放 $2$，因为后面合并结束之后就不一定是 $2$ 了。

设其为 $b(i, j)$。

转移同理 $b(i, j) = b(i - 1, j) \times a(i - 1, j - 1), B(i, j) = b(i, j) \times (1 - a(i - 1, j))$。
$$
f(i, 1) = \frac{\sum_{j = 2} ^ {50} f(i - 1, j) \times B(i - 1. j)}{\sum_{j = 2} ^ {50} B(i - 1, j)}
$$

------

之后我们需要做的就是矩阵加速递推了。

我们构造矩阵：
$$
\left[
\begin{matrix}
1 & f_{i, 1} & f_{i, 2} & \dots & f_{i, 50}
\end{matrix}
\right]
$$
之后转移矩阵就是把之前的转移放到每列上。

最终计算答案的时候就是：
$$
\sum_{k = 1} ^ {50} A(50, i) \times f(n, i)
$$

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
const int maxn = 50 + 5;
const int maxm = maxn << 1;
typedef double db;
const int N = 50;

struct Matrix {
    db a[maxn][maxn];
    Matrix(void) { memset(a, 0, sizeof(a)); }
    db * operator [] (const int &x) { return a[x]; }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i <= N; ++ i) {
            for(int j = 0; j <= N; ++ j)
            for(int k = 0; k <= N; ++ k) {
                res.a[i][j] += a[i][k] * z.a[k][j];
            }
        }
        return res;
    }
}F, tmp;

void ksm(Matrix &res, Matrix tmp, int mi) {
    while(mi) {
        if(mi & 1) res = res * tmp;
        mi >>= 1;
        tmp = tmp * tmp;
    }
}

int n, t;
db p;

db a[maxn][maxn], b[maxn][maxn];
db A[maxn][maxn], B[maxn][maxn];
db f[maxn][maxn];


signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, t); p = t * 1e-9;
//    printf("p = %.9lf\n", p);
    a[1][1] = p, a[1][2] = 1 - p;
    b[1][2] = 1 - p;
    for(i = 2; i <= N; ++ i) {
        a[i][1] = p, a[i][2] = 1 - p, b[i][2] = 1 - p;
        for(j = 2; j <= N; ++ j)
            a[i][j] += a[i - 1][j - 1] * a[i][j - 1],
            b[i][j] += a[i - 1][j - 1] * b[i][j - 1];
    }
    for(i = 1; i <= N; ++ i) {
        for(j = 1; j <= N; ++ j) {
            A[i][j] = a[i][j] * (1 - a[i - 1][j]);
            B[i][j] = b[i][j] * (1 - a[i - 1][j]);
        }
    }

//    for(i = 1; i <= 5; ++ i) {
//        for(j = 1; j <= 5; ++ j) {
//            printf("(%d ,%d) = %.9lf\n", i, j, a[i][j]);
//        }
//    }

    f[1][1] = 1, f[1][2] = 2;

    for(i = 2; i <= N; ++ i) {
        for(j = 2; j <= N; ++ j) {
            db tmp(0);
            f[i][j] = 0;
            for(int k = 1; k < j; ++ k)
                f[i][j] += f[i - 1][k] * A[i - 1][k],
                tmp += A[i - 1][k];
            f[i][j] = f[i][j] / tmp + j;
        }
        db tmp(0); f[i][1] = 0;
        for(j = 2; j <= N; ++ j)
            f[i][1] += f[i - 1][j] * B[i - 1][j],
            tmp += B[i - 1][j];
        f[i][1] = f[i][1] / tmp + 1;
    }

    if(n <= N) {
        db res(0);
        for(i = 1; i <= N; ++ i) res += f[n][i] * A[n][i];
        printf("%.15lf\n", res);
        return 0;
    }

    F.a[0][0] = tmp.a[0][0] = 1;
    for(i = 2; i <= N; ++ i) {
        db tp(0);
        for(j = 1; j < i; ++ j) tmp.a[j][i] += A[N][j], tp += A[N][j];
        for(j = 1; j < i; ++ j) tmp.a[j][i] /= tp;
        tmp.a[0][i] = i;
    }

    db tp(0);
    for(i = 2; i <= N; ++ i) tmp.a[i][1] += B[N][i], tp += B[N][i];
    for(i = 2; i <= N; ++ i) tmp.a[i][1] /= tp;
    tmp.a[0][1] = 1;

    for(i = 1; i <= N; ++ i) F.a[0][i] = f[N][i];

    ksm(F, tmp, n - N);

    db res(0);
    for(i = 1; i <= N; ++ i) res += F.a[0][i] * A[N][i];
    printf("%.15lf\n", res);

	return 0;
}

```




