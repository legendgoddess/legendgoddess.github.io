---
title: "[GXOI/GZOI2019]逼死强迫症"
date: 2021-09-23 19:33:20
tags: 
  - Dp
  - 数论
  - 矩阵
categories: 
  - 省选题解
---

<h3><center>P5303 [GXOI/GZOI2019]逼死强迫症</center></h3>

> [P5303 [GXOI/GZOI2019]逼死强迫症](https://www.luogu.com.cn/problem/P5303)

> 说实在的这题不难，但是我推 $\tt Dp$ 的时候却只和正解相差一点，最后还是看了题解。
>
> 感觉平时练习的时候还是需要再耐心一点，可能再过一过就出来了呢？

首先考虑 $\tt Dp$。

设 $f(i)$ 表示放了 $2 \times i$ 个方块，考虑不填 $1$ 的方块，那么答案就是 $f(i - 1) + f(i - 2)$。

如果填的话，我们考虑填的方法对于钦定了当前的位置的情况，另一边肯定是只有一种填法。而且对于中间的部分也是没有别的填法的，那么只有可能是在上一块填之前的位置。

我们考虑枚举上一块的位置：
$$
\begin{aligned}
&\sum_{j = 1} ^ {i - 1} Fib(j - 1) \\
=& \sum_{j = 1} ^ {i - 2} Fib(j) \\
=& Fib(i) - 1
\end{aligned}
$$

>  作者在[这篇博客](https://blog.csdn.net/sharp_legendgod/article/details/120436261)推导过这个结论。

可以发现递推式就是 $f(i) = f(i - 1) + f(i - 2) + 2 \times (Fib(i) - 1)$。

直接使用矩阵加速这个递推即可，我们注意样例里给出了 $f(1) = f(2) = 0$ 的答案，然后我们手推一下发现 $Fib(1) = Fib(2) = 1$。
$$
\left[
\begin{matrix}
f(i - 1) & f(i) & Fib(i) & Fib(i + 1) & 2 
\end{matrix}
\right]
\times
\left[
\begin{matrix}
0 & 1 & 0 & 0 & 0 \\
1 & 1 & 0 & 0 & 0 \\
0 & 0 & 0 & 1 & 0 \\
0 & 2 & 1 & 1 & 0 \\
0 & -1 & 0 & 0 & 1
\end{matrix}
\right]
$$

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
#define Getmod

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

#ifdef Getmod
const int mod  = 1e9 + 7;
template <int mod>
struct typemod {
    int z;
    typemod(int a = 0) : z(a) {}
    inline int inc(int a,int b) const {return a += b - mod, a + ((a >> 31) & mod);}
    inline int dec(int a,int b) const {return a -= b, a + ((a >> 31) & mod);}
    inline int mul(int a,int b) const {return 1ll * a * b % mod;}
    typemod<mod> operator + (const typemod<mod> &x) const {return typemod(inc(z, x.z));}
    typemod<mod> operator - (const typemod<mod> &x) const {return typemod(dec(z, x.z));}
    typemod<mod> operator * (const typemod<mod> &x) const {return typemod(mul(z, x.z));}
    typemod<mod>& operator += (const typemod<mod> &x) {*this = *this + x; return *this;}
    typemod<mod>& operator -= (const typemod<mod> &x) {*this = *this - x; return *this;}
    typemod<mod>& operator *= (const typemod<mod> &x) {*this = *this * x; return *this;}
    int operator == (const typemod<mod> &x) const {return x.z == z;}
    int operator != (const typemod<mod> &x) const {return x.z != z;}
};
typedef typemod<mod> Tm;
#endif

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

struct Matrix {
    Tm a[5][5];
    Matrix(void) {
        for(int i = 0; i < 5; ++ i) for(int j = 0; j < 5; ++ j)
            a[i][j] = 0;
    }
    void init() {
        for(int i = 0; i < 5; ++ i) a[i][i] = 1;
    }
    void clear() {
        for(int i = 0; i < 5; ++ i) for(int j = 0; j < 5; ++ j)
            a[i][j] = 0;
    }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < 5; ++ i) {
            for(int j = 0; j < 5; ++ j) {
                for(int k = 0; k < 5; ++ k) {
                    res.a[i][j] += a[i][k] * z.a[k][j];
                }
            }
        }
        return res;
    }
}F, tmp;

void ksm(Matrix &res,Matrix x,int mi) {
    while(mi) {
        if(mi & 1) res = res * x;
        mi >>= 1;
        x = x * x;
    }
}

int n;

void Solve() {
    r1(n);
    if(n <= 2) return puts("0"), void();
    tmp.clear();
    F.clear();

    tmp.a[1][0] = 1;
    tmp.a[0][1] = tmp.a[1][1] = 1, tmp.a[3][1] = 2, tmp.a[4][1] = mod - 1;
    tmp.a[3][2] = 1;
    tmp.a[2][3] = tmp.a[3][3] = 1;
    tmp.a[4][4] = 1;

    F.a[0][2] = 1, F.a[0][3] = 2, F.a[0][4] = 2;

    ksm(F, tmp, n - 2);
    printf("%d\n", F.a[0][1].z);
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, T;
    r1(T);
    while(T --) Solve();
	return 0;
}
```






