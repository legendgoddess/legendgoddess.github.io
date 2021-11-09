---
title: "[SCOI2009]迷路"
date: 2021-09-25 19:38:59
tags: 
  - 矩阵
categories: 
  - 省选题解
---

<h3><center>[SCOI2009]迷路</center></h3>

> [[SCOI2009]迷路](https://darkbzoj.tk/problem/1297)

### 思考过程

> 这个东西发现距离只有 $9$，记得 $\tt Lcm(1 \dots 9) = 2520$ 那么直接考虑暴力处理出来，之后对于每个 $T$ 进行分开计算即可。
>
> 显然这个很难写，但是同样是因为距离是 $9$ 那么我们每次只能对于 $T$ 增加 $1$ 不妨考虑直接对于每个点拆开计算。

### 题解

发现这个 $n$ 特别小，进行拆点矩阵快速幂，这里有几个细节就是因为是单向边，对于拆开的点肯定是距离小的连向距离大的。

这边给不是对于矩阵理解很清晰的神仙讲一下矩阵的细节：

笔者的写法是 $f_i$ 表示从 $0$ 开始到达点 $i$ 的方案数。

之后拆点之后也是如此，那么开始的矩阵就是：
$$
\left[
\begin{matrix}
f_{0, 1} & f_{0, 2} & \dots & f_{0, 9}  & f_{1, 1} & \dots & f_{n - 1, 9}
\end{matrix}
\right]
$$
之后考虑转移系数矩阵 $G$，对于 $i \to j$ 的转移系数的位置是 $G(i, j)$，那么如果有边就直接为 $1$ 即可。

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
const int mod  = 2009;
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
const int maxn = 90 + 5;
const int N = 90;
typedef long long ll;
int n, m;
char s[maxn];
ll T;
#define id(x, y) ( (x - 1) * 9 + y )

struct Matrix {
    int a[maxn][maxn];
    Matrix(void) { memset(a, 0, sizeof(a)); }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 1; i <= N; ++ i) for(int j = 1; j <= N; ++ j) {
            for(int k = 1; k <= N; ++ k) {
                res.a[i][j] = (res.a[i][j] + a[i][k] * z.a[k][j]) % mod;
            }
        }
        return res;
    }
}G, F;

void ksm(Matrix &res, Matrix tmp,int mi) {
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
    r1(n, T);
    for(i = 1; i <= n; ++ i)
        for(j = 2; j <= 9; ++ j)
            G.a[id(i, j - 1)][id(i, j)] = 1;
    for(i = 1; i <= n; ++ i) {
        scanf("%s", s + 1);
        for(j = 1; j <= n; ++ j) if(s[j] != '0') {
            int x = s[j] ^ 48;
            G.a[id(i, x)][id(j, 1)] = 1;
        }
    }
    F.a[1][1] = 1;
    ksm(F, G, T);
    int ans = F.a[1][id(n, 1)];
    printf("%d\n", ans);
	return 0;
}
```




