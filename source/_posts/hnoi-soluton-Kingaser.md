---
title: [HNOI2015]亚瑟王
date: 2021-10-07 14:03:08
tags: 
  - 期望
  - Dp
categories: 
  - 省选题解
---

<h3><center>[HNOI2015]亚瑟王</center></h3>

> [[HNOI2015]亚瑟王](https://www.luogu.com.cn/problem/P3239)

根据期望的线性性质，我们考虑每一张牌的贡献。也就是每一张牌被使用的概率。

显然第一张牌使用的概率就是 $1 - (1 - p_1) ^ r$。

但是发现之后的牌的使用依赖于前面的牌的使用，因为如果前面有 $j$ 张牌被使用了，相当于有 $j$ 轮对于当前牌是无效的。

我们考虑进行 $\tt dp$，设 $f(i, j)$ 表示前 $i$ 张使用了 $j$ 张牌的概率。
$$
f(i, j) = 
\begin{cases}
f(i - 1, j) \times \left(1 - p_i\right)^{r - j} \\
f(i -1, j - 1) \times \left[1 - (1 - p_i)^{r - j + 1}\right]
\end{cases}
$$

$$
F(i) = 
\begin{aligned}
\sum_{k \le i} f(i - 1,k) \times \left[1 - (1 - p_i)^{r -j}\right]
\end{aligned}
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
const int maxn = 220 + 5;
const int maxm = maxn << 1;

double f[maxn][maxn];
int n, r, d[maxn];
double ksm(double x,int mi) {
    double res(1);
    while(mi) {
        if(mi & 1) res = res * x;
        mi >>= 1;
        x *= x;
    }
    return res;
}

double p[maxn][maxn], pp[maxn];
double F[maxn];

void Solve() {
    int i, j;
    r1(n, r);
    for(i = 1; i <= n; ++ i) F[i] = 0;
    for(i = 1; i <= n; ++ i) scanf("%lf%d", &pp[i], &d[i]);
    for(i = 0; i <= n + 2; ++ i) for(j = 0; j <= n + 2; ++ j) f[i][j] = p[i][j] = 0;
    for(i = 1; i <= n; ++ i) {
        p[i][0] = 1;
        for(j = 1; j <= r + 1; ++ j)
            p[i][j] = p[i][j - 1] * (1 - pp[i]);
    }
    f[0][0] = 1;
    for(i = 1; i <= n; ++ i) for(j = 0; j <= i; ++ j) {
        f[i][j] = f[i - 1][j] * p[i][r - j];
        if(j > 0)
            f[i][j] += f[i - 1][j - 1] * (1 - p[i][r - j + 1]);
    }
    F[1] = 1 - p[1][r];
    for(i = 2; i <= n; ++ i) {
        for(j = 0; j < i; ++ j)
            F[i] += f[i - 1][j] * (1 - p[i][r - j]);
    }
    double ans(0);
    for(i = 1; i <= n; ++ i) ans += F[i] * d[i];
    printf("%.12lf\n", ans);
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, T;
    r1(T); while(T --) Solve();
	return 0;
}
```




