---
title: CF498E Stairs and Line
date: 2021-09-25 20:43:36
tags: 
  - 矩阵
  - Dp
categories: 
  - CF题解
---

<h3><center>CF498E Stairs and Lines</center></h3>

> [CF498E Stairs and Lines](https://www.luogu.com.cn/problem/CF498E)

肯定是状压，而且状压的就是轮廓线，我们考虑同时状压横着和竖着的情况。然后肯定需要进行矩阵快速幂复杂度还是很高的。

但是我们考虑我们当前位置和之前位置进行转移的时候使用的是竖着的线，而且横着的线仅仅在当前转移出现了，意味着肯定不会影响后面的计算。那么我们不妨枚举轮廓线，这样复杂度就是直接少了 $2^{7^3}$。

然后说几个细节，具体状态转移就自己推吧。我的写法是考虑当前位置和后面位置的衔接，对于也就是 $w_1$ 我们特殊处理一下 $w_0$ 也就是只有一个位置（存在竖线）是合法的。不然的话因为没有点所以只能是 $0$。

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
const int mod  = 1e9 + 7;// orz
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

#define int long long
const int maxn = 7e3 + 5;
const int maxm = maxn << 1;
const int mod = 1e9 + 7;
struct Matrix {
    int n, m, a[128][128];
    Matrix(void) { memset(a, 0, sizeof(a)); }
//    Matrix operator * (const Matrix &z) const {
//        Matrix res; res.n = z.n, res.m = m;
//        for(int i = 0; i <= res.n; ++ i) {
//            for(int j = 0; j <= res.m; ++ j) {
//                for(int k = 0; k <= n; ++ k)
//                    res.a[i][j] = (res.a[i][j] + 1ll * a[k][j] * z.a[i][k] % mod) % mod;
//            }
//        }
//        return res;
//    }
    friend Matrix operator * (const Matrix &a, const Matrix &b) {
        Matrix res; res.n = b.n, res.m = a.m;
        for(int i = 0; i <= res.n; ++ i) {
            for(int j = 0; j <= res.m; ++ j) {
                res.a[i][j] = 0;
                for(int k = 0; k <= a.n; ++ k)
                    res.a[i][j] = (res.a[i][j] + 1ll * a.a[k][j] * b.a[i][k] % mod) % mod;
            }
        }
        return res;
    }
}Ans, S;

void ksm(Matrix &res, Matrix tmp,int mi) {
    while(mi) {
        if(mi & 1) res = res * tmp;
        mi >>= 1;
        tmp = tmp * tmp;
    }
}

int dp[maxn][128], c[128][128], w[10];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    Ans.n = 127;
    int mx(0);
    for(i = 1; i <= 7; ++ i) {
        r1(w[i]);
        if(w[i]) mx = i;
    }
    int now(0), tmp(0);
    if(w[1] == 0) dp[now][1] = 1;
    else dp[now][0] = 1;
    c[0][0] = c[1][0] = c[0][1] = 1;
    for(i = 1; i <= mx; ++ i) {
        tmp |= (1 << i);
        if(w[i]) {
            Ans.n = S.n = S.m = (1 << i) - 1;
            for(j = 0; j < (1 << i); ++ j)
                Ans.a[j][0] = dp[now][j];
            for(j = 0; j < (1 << i); ++ j) {
                for(int k = 0; k < (1 << i); ++ k) {
                    S.a[j][k] = c[j][k];
                }
            }
            ksm(Ans, S, w[i] - 1);
            for(j = 0; j < (1 << i); ++ j)
                dp[now][j] = Ans.a[j][0];
        }
        memset(c, 0, sizeof(c));
        if(w[i + 1]) {
            ++ now;
            for(int k = 0; k < (1 << (i + 1)); ++ k)
            for(int s = 0; s < (1 << i); ++ s)
            for(j = 0; j < (1 << (i + 1)); ++ j) {
                if(!(j & k & (s | (1 << i)) & ((s << 1) | 1)))
                    c[j][k] ++;
                if(!((j | tmp) & k & (s | (1 << i)) & ((s << 1) | 1)))
                    dp[now][k] = (dp[now][k] + dp[now - 1][j]) % mod;
            }
            tmp = (1 << (i + 1));
        }
    }
    printf("%lld\n", dp[now][(1 << mx) - 1]);
	return 0;
}
```




