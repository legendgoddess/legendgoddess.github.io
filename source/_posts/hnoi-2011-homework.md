---
title: "[HNOI2011]数学作业"
date: 2021-09-25 14:31:13
tags: 
  - 矩阵
  - Dp
categories: 
  - 省选题解
---

<h3><center>[HNOI2011]数学作业</center></h3>

> [[HNOI2011]数学作业](https://darkbzoj.tk/problem/2326)。

直接考虑 $\tt Dp$ 显然 $f_i = f_{i - 1} \times 10^{x} + i$。那么我们直接将其分段进行计算即可。

具体的矩阵：
$$
\left[
\begin{matrix}
1 & i & f_i
\end{matrix}
\right]
$$

$$
\left[
\begin{matrix}
1 & 1 & 0 \\
0 & 1 & 1 \\
0 & 0 & 10^x
\end{matrix}
\right]
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
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;
typedef long long ll;
ll mod, n;

struct Matrix {
    int a[3][3];
    Matrix(void) { memset(a, 0, sizeof(a)); }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < 3; ++ i) {
            for(int j = 0; j < 3; ++ j) {
                for(int k = 0; k < 3; ++ k) {
                    res.a[i][j] = (res.a[i][j] + 1ll * a[i][k] * z.a[k][j] % mod) % mod;
                }
            }
        }
        return res;
    }
    void print() {
        for(int i = 0; i < 3; ++ i) {
            for(int j = 0; j < 3; ++ j) printf("(%d, %d) = %d\n", i, j, a[i][j]);
            puts("");
        }
    }
}tmp, F;
ll pw[20];

void ksm(Matrix &res, Matrix tmp,ll mi) {
//    if(mi < 0) return ;
//    printf("mi = %lld\n", mi);
    while(mi) {
        if(mi & 1) res = res * tmp;
        mi >>= 1;
        tmp = tmp * tmp;
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    pw[0] = 1;
    for(i = 1; i <= 18; ++ i) pw[i] = pw[i - 1] * 10;
    r1(n, mod);

    F.a[0][0] = 1, F.a[0][1] = 1, F.a[0][2] = 0;

    tmp.a[0][0] = 1;
    tmp.a[0][1] = tmp.a[1][1] = 1;
    tmp.a[1][2] = 1;

    for(i = 1; i <= 18; ++ i) {
        tmp.a[2][2] = pw[i] % mod;
//        tmp.print();
        if(pw[i] - 1 >= n) {
//                puts("XX");
            ksm(F, tmp, n - pw[i - 1] + 1);
            break;
        }
        else {
            ksm(F, tmp, pw[i] - pw[i - 1]);
        }
    }
//    for(i = 0; i < 3; ++ i) printf("%d : %d\n", i, F.a[0][i]);
//    puts("\n --- \n");
    printf("%d\n", F.a[0][2]);
	return 0;
}
```




