---
title: 差分约束浅谈
date: 2021-10-14 13:38:28
tags:
  - 图论
  - 差分约束
categories:
  - 算法
---

<h3><center>差分约束浅谈</center></h3>

>  还是挺难的一个东西。

@[toc]
---

### 引入

考虑一个限制 $A_i \le x_i + C_i$ 其中 $x_i$ 是一个给定的值。

$A, C$ 表示两个位置之间的关系。

发现对于同一个 $A_i$ 总共有若干个限制，那么也就是意味着 $A = \min_i(x_i + C_i)$。

发现这个东西就是最短路的更新。

我们换一种角度来思考，对于每一个 $C_i$ 其对应的限制就是 $C_i \ge A_i - x_i$ 也就是意味着 $C_i = \max(A_i - x_i)$ 这个就是最长路了。

所以说在题目没有给一些限制的时候我们可以将最长路和最短路相互转换。

----

### 实现

常说这个东西是需要使用 $\tt SPFA$ 的，事实上 $\tt Floyd, Dijkstra$ 也是完全可以的，最主要的问题是 $\tt Bellman-Fold$ 这类算法是可以判断负环的，我们可以清楚得知是否有解。

> 当然如果说跑最长路也是可以的。

#### 负环

如果说一个点入队次数超过所有点的次数，那么肯定是存在环了。

不妨考虑其所在的路径的长度是 $ct_i$，有更新 $ct_u + 1\to ct_i$ 其中 $u \to i$。

---

### 最长路和最短路的区别

最短路是符合条件的最大解，最长路是符合条件的最小解。

> 这个具体证明可以找网上的。

更多的时候需要根据题目来判断求的是最大解还是最小解。

----

## 常见例题：

[板子](https://www.luogu.com.cn/problem/P5960)

---

### 乘法化加法

<h3><center>P4926 [1007]倍杀测量者</center></h3>

> [P4926 [1007]倍杀测量者](https://www.luogu.com.cn/problem/P4926)

限制是诸如：

- $A(K + T) \le B$ 的形式的。

我们考虑两边都取对数即可，也就是变成了 $\log A + \log(K + T) \le \log B$。

----

### 最大最小值的应用

<h3><center>P2474 [SCOI2008]天平</center></h3>

> [P2474 [SCOI2008]天平](https://www.luogu.com.cn/problem/P2474)

题目说需要确定的值才可能，我们考虑同时计算一下最小值和最大值，之后直接考虑将柿子变成 $A - x\ B - y$ 的形式，然后两边枚举比较即可。

复杂度是 $O(n^3)$。

----

### 转换

<h3><center>AT5147 [AGC036D] Negative Cycle</center></h3>

> [AT5147 [AGC036D] Negative Cycle](https://www.luogu.com.cn/problem/AT5147)

图中没有负环，说明 $\tt SPFA$ 是有解的，我们不妨考虑 $d_i$ 表示点 $i$ 距离点 $0$ 的距离。

>我们新创建一个点 $0$ 来表示第一个位置之前的点。

显然有限制 $d_i \ge d_{i + 1}, 0 \le d_i - d_{i - 1} \le 1$，我们选取严格的即可。

考虑 $a_i = d_i - d_{i + 1}$ 显然 $a$ 是 $0, 1$ 其中一个。

之后考虑可以考虑每个点是 $0$ 还是 $1$，考虑一个 $\tt dp$，设 $f(i, j)$ 表示位置 $i$ 是 $1$，上一个是 $1$ 的位置是 $j$。

转移显然是枚举一个 $k$ 进行转移，然后考虑什么时候是合法的，因为相差是 $1$ 所以不能有 $-1$ 的边，而且对于 $[k, j], [j, i]$ 这两个位置之间如果存在左边连接右边的 $-1$ 边是没有影响的，如果存在 $[j, n]$ 的 $1$ 边会导致后面的能链接前面的位置导致结果更小，这个也是不行的。

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

#define int long long
const int maxn = 5e2 + 5;
const int maxm = maxn << 1;

int A[maxn][maxn], B[maxn][maxn], a[maxn][maxn];
int n;
int Val(int k,int j,int i) {
    return B[j + 1][i] + a[n][j] - a[n][k] - a[i][j] + a[i][k];
}
int f[maxn][maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= n; ++ j) if(i != j) r1(a[i][j]);
    }
    for(j = 1; j <= n; ++ j)
        for(i = j; i >= 1; -- i)
            B[i][j] = B[i + 1][j] + a[i][j] + B[i][j - 1] - B[i + 1][j - 1];
    for(i = 1; i <= n; ++ i) for(j = 1; j <= n; ++ j)
        a[i][j] += a[i - 1][j] + a[i][j - 1] - a[i - 1][j - 1];
    int ans(1e18);
    for(i = 1; i < n; ++ i) {
        f[i][0] = Val(0, 0, i);
        ans = min(ans, f[i][0] + Val(0, i, n));
        for(j = 1; j < i; ++ j) {
            f[i][j] = 1e18;
            for(int k = 0; k < j; ++ k) {
                f[i][j] = min(f[i][j], f[j][k] + Val(k, j, i));
            }
            ans = min(ans, f[i][j] + Val(j, i, n));
        }
    }
    printf("%lld\n", ans);

	return 0;
}
```



