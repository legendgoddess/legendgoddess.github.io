---
title: "[HNOI2002]公交车路线"
date: 2021-09-25 19:50:22
tags: 
  - 矩阵
categories: 
  - 省选题解
---

<h3><center>[HNOI2002]公交车路线</center></h3>

> [[HNOI2002]公交车路线](https://www.luogu.com.cn/problem/P2233)

> 这题和没有一样。

直接按照题意建立转移矩阵即可。

笔者的答案矩阵是：
$$
\left[
\begin{matrix}
f_{A} & f_B & f_C & \dots & f_H
\end{matrix}
\right]
$$
其中 $f_i$ 表示经过了 $n$ 次操作从 $A$ 走到 $i$ 的方案数。

不妨设转移矩阵为 $G$。那么对于一个 $i \to j$ 的情况对应到转移矩阵中就是 $G(i, j)$ 。

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
const int mod  = 1e3;
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

int n;
struct Matrix {
    Tm a[8][8];
    Matrix(void) { for(int i = 0; i < 8; ++ i) for(int j = 0; j < 8; ++ j) a[i][j] = 0; }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < 8; ++ i) for(int j = 0; j < 8; ++ j) {
            for(int k = 0; k < 8; ++ k)
                res.a[i][j] += a[i][k] * z.a[k][j];
        }
        return res;
    }
}F, G;

void ksm(Matrix &res, Matrix x,int mi) {
    while(mi) {
        if(mi & 1) res = res * x;
        mi >>= 1;
        x = x * x;
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    F.a[0][0] = 1;
    for(i = 1; i < 8; ++ i)
        G.a[i - 1][i] = G.a[i][i - 1] = 1;
    G.a[7][0] = G.a[0][7] = 1;
    G.a[4][5] = 0;
    G.a[4][3] = 0;
    ksm(F, G, n);
    printf("%d\n", F.a[0][4].z);
	return 0;
}
```




