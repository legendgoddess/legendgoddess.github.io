---
title: Luogu3758 [TJOI2017]可乐
date: 2021-07-28 18:30:25
tags:
  - Dp
categories:
  - 算法
---
[P3758 [TJOI2017]可乐](https://www.luogu.com.cn/problem/P3758)

首先考虑一个 $Dp$。会想到 $f(t, i, j)$ 表示在时刻 $t$ 走到位置 $(i, j)$ 的方案数。但是因为我们是从 $1$ 开始走。那么完全可以设 $f(t, i)$ 表示从 $1 \to i$ 在时刻 $t$ 的方案数。转移的话就枚举之后可以走到的位置进行转移。

然后处理一下爆炸的情况，可以建立一个新的节点，之后进行转移，然后每个点只能进入不能出来。

我们发现本质上我们的 $t$ 意味着死亡时间最晚是 $t$ 所以之前的状态需要继承。然后要对于新建的节点再来一个自环，进行转移。

```cpp
#include <bits/stdc++.h>
using namespace std;

#define Fread
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

#ifdef Getmod
const int mod  = 2017;
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

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 30 + 5;
const int maxm = 1e6 + 5;

int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[(int)1e5];
inline void add(const int &u, const int &v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

int n, m;
Tm f[2][maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v;
        r1(u, v);
        add(u, v), add(v, u);
    }
    for(i = 1; i <= n; ++ i) add(i, i), add(i, n + 1);
    add(n + 1, n + 1);
    int t; r1(t);
    f[0][1] = 1;
    for(i = 1; i <= t; ++ i) {
        int now(i & 1), bef(now ^ 1);
        for(j = 1; j <= n + 1; ++ j) f[now][j] = 0;
        for(j = 1; j <= n + 1; ++ j) {
            for(int k = head[j];k;k = edg[k].next) {
                int to = edg[k].to;
                f[now][to] += f[bef][j];
            }
        }
    }
    Tm ans(0);
    for(i = 1; i <= n + 1; ++ i) ans += f[t & 1][i];
    printf("%d\n", ans.z);
	return 0;
}

```

当然以上只是卡常可以过去，我们对其建立一个转移矩阵即可。

然后我们考虑对于一个邻接矩阵的 $k$ 次，意味这什么呢？本质上就是进行了 $k$ 次 $floyd$ 更新。也就是说矩阵 $(i, j)$ 就是表示之前说的信息。

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

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 30 + 5;
const int maxm = maxn << 1;

int n, m;
struct Matrix {
    int a[maxn][maxn];
    Matrix(void) {memset(a, 0, sizeof(a));}
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i <= n; ++ i) {
            for(int j = 0; j <= n; ++ j) {
                for(int k = 0; k <= n; ++ k) {
                    res.a[i][j] = (res.a[i][j] + a[i][k] * z.a[k][j] % 2017) % 2017;
                }
            }
        }
        return res;
    }
}vc;

Matrix ksm(Matrix &z, int mi) {
    Matrix res;
    for(int i = 0; i <= n; ++ i) res.a[i][i] = 1;
    while(mi) {
        if(mi & 1) res = res * z;
        mi >>= 1;
        z = z * z;
    }
//    for(int i = 0; i <= n; ++ i) {
//        for(int j = 0; j <= n; ++ j) {
//            printf("vc[%d][%d] = %d\n", i, j, res.a[i][j]);
//        }
//    }
    return res;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v;
        r1(u, v);
        vc.a[u][v] = vc.a[v][u] = 1;
    }
    for(i = 0; i <= n; ++ i) vc.a[i][0] = 1;
    for(i = 0; i <= n; ++ i) vc.a[i][i] = 1;
    int t; r1(t);
    Matrix res = ksm(vc, t);
    int ans(0);
    for(i = 0; i <= n; ++ i) ans = (ans + res.a[1][i]) % 2017;
    printf("%d\n", ans);
	return 0;
}
```


