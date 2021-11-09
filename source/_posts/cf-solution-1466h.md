---
title: CF1466H Euclid's nightmare
date: 2021-08-26 19:34:11
tags:
  - Dp
  - 数论
  - 图论
categories:
  - CF题解
---

### [CF 1466H](https://codeforces.com/problemset/problem/1466/H)

> 话说某谷的翻译直接讲了第一步 $\dots$

**题目大意：**

总共有 $n$ 个人，每个人有一个长度为 $n$ 的排列，其中越靠前的数，其越喜欢。

对于一个序列 $A$，一开始分配的方案是 $i$ 号人分配到 $A_i$。

称一个序列 $A$ 是合法的，当且仅当不存在两个或者多个人交换 $A_i$ 使得有人得到更加喜欢的数。

给定一个数组 $A_i$ 求出有多少个排列，满足 $A$ 是合法的。

> 排列指的是每个人都喜欢序列。

----

发现如果一个序列不合法肯定存在一个置换使得有人可以更优。

置换本质就是环，我们考虑建图，对于一个 $j$ 在 $A_j$ 前面的点向其连接黑色的边。当然我们有 $j \to A_j$ 这个是白色边。如果存在一个环其中有黑色边就不合法。

我们只考虑确定的位置就是 $j \to A_j$ 的边。

如果点 $i$ 连接入了 $x$ 条黑边那么方案数就是 $g(x) = x! \times (n - x - 1) !$ 。

这个本质就是一个 $n$ 个点有标号的 $DAG$ 计数问题。

设 $dp(i)$ 就是答案。

我们枚举一下总共有多少个点是没有出度的。
$$
dp(i) = \sum_{j = 1} ^ i dp(i - j) \times calc(i - j, j)
$$
其中 $calc(a, b)$ 表示 $a$ 中的点向 $b$ 中连边的方案。

但是显然这个会算重复，我们考虑可能之前也会有点没有出度，那么我们加一个容斥系数 $(-1) ^ {j + 1}$ 即可。
$$
calc(i, 1) = \sum_{j = 0} ^ i \binom{i}{j} j! \times (n - j - 1)! \\
calc(i, j) = calc(i, j - 1) \times calc(i, 1)
$$
但是如果直接进行状压其数量级太大了，我们考虑将其减少一点。

对于相同的环我们可以将其放到一起进行计算。

其实可以只记录大小为 $1, 2$ 的环。之后直接状压。

---

我们考虑根据图的方案数进行 $dp$。

之后考虑每一个大小的环进行转移即可。

> 代码里有注释。

```cpp
#include <bits/stdc++.h>
using namespace std;

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
const int maxn = 40 + 5;
const int maxm = 2e3 + 5;

Tm fac[maxn], f[maxn][maxn], inv[maxn];

Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}

Tm C(int a,int b) {
    if(a < b) return 0;
    return fac[a] * inv[b] * inv[a - b];
}

int n, a[maxn], b[maxn], s[maxn];
int vis[maxn];

void init() {
    int i, j;
    fac[0] = 1;
    for(i = 1; i <= n; ++ i) fac[i] = fac[i - 1] * i;
    inv[n] = ksm(fac[n], mod - 2);
    for(i = n - 1; ~ i; -- i) inv[i] = inv[i + 1] * (i + 1);
    for(i = 0; i < n; ++ i) {
        f[i][0] = 1;
        for(j = 0; j <= i; ++ j)
            f[i][1] += fac[j] * fac[n - j - 1] * C(i, j);
//        printf("f[%d][1] = %d\n", i, f[i][1].z);
        for(j = 2; j <= n; ++ j)
            f[i][j] = f[i][1] * f[i][j - 1];
    }
}

Tm dp[maxm];
Tm w[maxm];
int p[maxm], siz[maxm];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    init();
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= n; ++ i) if(!vis[i]) {
        int ct(0);
        for(int j = i; !vis[j]; j = a[j])
            vis[j] = 1, ++ ct;
        b[ct] ++;
    }
    s[0] = 1;
    for(i = 1; i <= n; ++ i) s[i] = s[i - 1] * (b[i] + 1);
//    printf("s[n] = %d\n", s[n]);
    dp[0] = 1;
    for(int x = 1; x < s[n]; ++ x) {
        int t = 1;
        p[1] = 0, w[1] = mod - 1;
        for(i = 1; i <= n; ++ i) {
            int tmp = t, v = (x / s[i - 1]) % (b[i] + 1);
            /*
            我们考虑长度为 i 的环进行转移
            那么本质上就是 x / s[i] 之后的剩余部分是可以进行转移的。
            但是会出现 x < s[i], x > s[i - 1] 的情况，这些部分是可以转移的，但是直接除以就无法计算
            我们让其除以 s[i - 1] 即可
            */
            siz[x] += i * v;
            for(j = 1; j <= v; ++ j) {
                for(int h = 1; h <= tmp; ++ h) {
                    p[++ t] = p[h] + j * s[i - 1];
                    w[t] = w[h] * (j % 2 ? mod - 1 : 1) * C(v, j);
                }
            }
        }
//        printf("siz[%d] = %d\n", x, dp[x - p[].z);
        for(i = 2; i <= t; ++ i) {
            dp[x] += dp[x - p[i]] * w[i] * f[siz[x] - siz[p[i]]][siz[p[i]]];
        }
    }
    Tm ans = dp[s[n] - 1];
    printf("%d\n", ans.z);
	return 0;
}
```




