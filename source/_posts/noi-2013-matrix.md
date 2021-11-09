---
title: "[NOI2013] 矩阵游戏"
date: 2021-09-25 18:48:55
tags: 
  - 数论
  - 矩阵
  - Dp
categories: 
  - NOI题解
---

<h3><center>[NOI2013] 矩阵游戏</center></h3>

> [[NOI2013] 矩阵游戏](https://uoj.ac/problem/124)

> 各位我是弱智石锤了。

题目可能不是很难但是有点细节要注意。

首先考虑行和列我们分别来看：

- 对于列的情况我们只需要前一项即可递推。
- 对于行的情况我们只需要上一项也可递推。

那么本质上题目就提示我们可以只保留一项进行矩阵快速幂。

不妨设答案矩阵是
$$
\left[
\begin{matrix}
1 & f_m
\end{matrix}
\right]
$$
我们考虑首先递推同一行之后再递推列，最后一行单独拿出来处理。
$$
\left[
\left[
\begin{matrix}
1 & b \\
0 & a
\end{matrix}
\right] ^ {m - 1} \times 
\left[
\begin{matrix}
1 & d \\
0 & c
\end{matrix}
\right]
\right] ^ {n - 1} \times 
\left[
\begin{matrix}
1 & b \\
0 & a
\end{matrix}
\right] ^{m - 1}
$$
**注意** 矩阵乘法**不满足**交换律，所以我们矩阵必须按照顺序进行计算。

之后还有一个问题就是 $n, m$ 实际上是很大的，但是对于矩阵不一定有费马小定理。

我们考虑对于第一个矩阵的 $mod - 1$ 次幂得到的矩阵：
$$
\left[
\begin{matrix}
1 & b \times (a^{mod - 2} + a^{mod - 3} +\dots + a^0) \\
0 & a^{mod - 1}
\end{matrix}
\right]
$$
我们发现 $a^{mod - 1}$ 直接使用费马小定理得到是 $1$。

对于上面 $b \times (a^{mod - 2} + a^{mod - 3} +\dots + a^0)$ 的部分，我们将里面的东西拿出来得到 $\dfrac{a^{mod - 1} - 1} {a - 1}$ 。

如果 $a = 1$ 呢？直接带入可以得到当幂是 $mod$ 的时候上面的部分是 $b \times mod$ 那么也是单位矩阵了。

我们直接对于这个取模即可。

之后考虑 $n$ 的限制，同理我们将
$$
\left[
\begin{matrix}
1 & b \\
0 & a
\end{matrix}
\right] ^ {m - 1} \times 
\left[
\begin{matrix}
1 & d \\
0 & c
\end{matrix}
\right]
$$
这个矩阵拿出来，发现左上角同样是 $1$ 我们可以通过相同的方式得到。

------

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
int n, m;
const int mod = 1e9 + 7;
struct Matrix {
    int a[2][2];
    Matrix(void) { memset(a, 0, sizeof(a)); }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < 2; ++ i) {
            for(int j = 0; j < 2; ++ j) {
                for(int k = 0; k < 2; ++ k) {
                    res.a[i][j] = (res.a[i][j] + 1ll * a[i][k] * z.a[k][j] % mod) % mod;
                }
            }
        }
        return res;
    }
}F, tmp1, tmp2;
string sn, sm;
int A, B, C, D;

Matrix ksm(Matrix x,int mi) {
    Matrix res;
    res.a[0][0] = res.a[1][1] = 1;
    while(mi) {
        if(mi & 1) res = res * x;
        mi >>= 1;
        x = x * x;
    }
    return res;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    cin >> sn >> sm;
    r1(A, B, C, D);// A <= C
    int mod1 = ( (A == 1) ? mod : mod - 1 );
    for(i = 0; i < sm.size(); ++ i)
        m = ( 10ll * m + (sm[i] ^ 48) ) % mod1;

//    printf("%d %d\n", n, m);

    F.a[0][0] = F.a[0][1] = 1;
    tmp1.a[0][0] = 1, tmp1.a[1][0] = 0;
    tmp1.a[0][1] = B, tmp1.a[1][1] = A;
    tmp2.a[0][0] = 1, tmp2.a[1][0] = 0;
    tmp2.a[0][1] = D, tmp2.a[1][1] = C;
    tmp1 = ksm(tmp1, m < 1 ? m + mod1 - 1 : m - 1);
    tmp2 = tmp1 * tmp2;
    mod1 = (tmp2.a[1][1] == 1 ? mod : mod - 1);
    for(i = 0; i < sn.size(); ++ i)
        n = ( 10ll * n + (sn[i] ^ 48) ) % mod1;
    F = F * ksm(tmp2, n < 1 ? n + mod1 - 1 : n - 1) * tmp1;
    printf("%d\n", F.a[0][1]);
	return 0;
}
/*
34578734657863487 38465873465876348 1 23734637 3892734 3849

467549493
*/
```




