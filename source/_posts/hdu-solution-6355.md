---
title: Hdu6355 Fireflies 题解
date: 2021-10-09 15:34:10
tags:
  - 模拟
  - 数论
  - Dp
categories:
  - HDU题解
---

<h3><center>Hdu6355 Fireflies</center></h3>

> [Hdu6355 Fireflies](https://acm.hdu.edu.cn/showproblem.php?pid=6355)

> 发现网上没有什么题解 $\dots$

**题目描述**

> 在一个 $n$ 维度的超立方体，每一维度的大小是 $p_i$。你可以在任意位置放一个萤火虫，萤火虫每次只能在一个维度移动一个单位，对于 $x_i \to y_i$ 需要保证 $x_i + 1 = y_i$，而且不能超过超立方体。
>
> 求最开始最少放多少个，才能保证存在一种方案，对于每一个单位空间都有一个萤火虫遍历它。

---

我们考虑一下，这个是一个覆盖问题，是否可以用上 $dilworth$ 定理。显然最小链覆盖就是最大的反链。考虑对于一个 $n$ 种元素的集合，能选择最多多少个元素两两不包含。

这个问题很简单就是 $\dbinom{n}{\lfloor\dfrac{n}{2}\rfloor}$。

> $Sperner$ 定理。

对于这个定理我们其有一个推论就是对于多重集 $S$ 偏序是 $\subseteq$，每个元素出现 $a_i$ 次。最大反链的个数就是大小是 $\dfrac{\sum{a_i}}{2}$ 的集合数量。

我们考虑上面的问题，也就是选择一个集合，集合上面的点两两不可互相到达，如果两个点可以互相到达那么肯定存在某个点的每一维坐标都 $\le$ 另一个点，显然这个是充要的。

我们仿照之前定理的推论，也就是每一维度的坐标只能选择一半，也就是 $\dfrac{\sum_{} p_i - 1}{2} = m$。

> 具体的证明可以看文章末尾。

容易知道左右两边的集合是对称的，我们不妨计数右边的集合可以少讨论一些边界条件， $m = \lfloor \dfrac{\sum p_i + 1}{2}\rfloor$

那么就是求 $\sum x_i = m, 1 \le x_i \le p_i$ 的方案数。

----

发现 $n = 32$ 我们考虑 $\tt meet\ in \ the\ middle$。

我们可以直接进行容斥得到 $\sum_{S} (-1)^{|S|} \dbinom{m - 1 - \sum_{i \in S} a_i}{n - 1}$。

但是进行两个位置合并的时候需要知道 $\sum_{S} \sum_{S1}$ 需要 $\sum_{i \in S \text{ or } i \in S1} a_i$ 我们不妨将 $\sum_{i \in S} a_i$ 当做一个未知量 $x$。

发现是一个关于 $x$ 的 $n - 1$ 次方程。

也就是将 $\dbinom{m - 1 - x}{n - 1}$ 拆解成关于 $x$ 的多项式，我们将 $\dfrac{1}{(n - 1)!}$ 单独分开。

设 $f(i, j)$ 表示考虑了 $i$ 个数幂次是 $j$ 的系数（那个 $-1$ 是放到之后加的）。
$$
f(i, j) = - f(i - 1, j - 1)  + f(i - 1, j) \times (m - i)
$$

---

之后我们直接通过前缀和进行维护即可，但是会出现一个问题有些 $\dbinom{m - 1 - x}{n - 1}$ 其实是没有值的，但是我们拆开计算会导致其有值。我们可以对于两边都维护一个前缀和，通过排序然后移动双指针即可。

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
const int maxn = 32 + 5;
const int maxm = (1 << 16) + 5;
const int N = 34;

Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}
Tm fac[maxn], inv[maxn];
int ct[maxm];

Tm C(int a,int b) {
    if(a < b) return 0;
    return fac[a] * inv[b] * inv[a - b];
}

void init(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = fac[i - 1] * i;
    inv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) inv[i] = inv[i + 1] * (i + 1);
    for(int i = 1; i < maxm; ++ i) ct[i] = ct[i >> 1] + (i & 1);
}
int n; long long m(0);
Tm mm;
int p[maxn];
Tm f[maxn][maxn], rsf[maxn];
void Work1() {
    f[0][0] = 1;
    for(int i = 1; i < n; ++ i) {
        for(int j = 1; j < n; ++ j) f[i][j] = Tm(0) - f[i - 1][j - 1];
        for(int j = 0; j < n; ++ j)
            f[i][j] += f[i - 1][j] * (mm - i);
    }
    for(int i = 0; i < n; ++ i) rsf[i] = inv[n - 1] * f[n - 1][i];
//    for(int i = 0; i < n; ++ i) printf("%d : %d %d\n", i, rsf[i].z, f[n - 1][i].z);
}

Tm ans[maxn];

struct Node {
    long long sum;
    Tm a[maxn];
    Node(void) { sum = 0; }
    int operator < (const Node &z) const {
        return sum < z.sum;
    }
    Tm& operator [] (const int &x) { return a[x]; }
    const Tm& operator [] (const int &x) const { return a[x]; }
}xl[maxm], xr[maxm];

Tm presum[maxn];

int Work2() {
    int i, j;
    int mid = (n + 1) / 2, mid1 = n - mid;
    Tm vis[2] = {1, mod - 1};
    for(int S = 0; S < (1 << mid); ++ S) {
        long long tmp(0);
        for(int i = 0; i < mid; ++ i) if((S >> i) & 1) tmp += p[i + 1];
//        printf("S = %d, tmp = %lld\n", S, tmp);
        xl[S].sum = tmp, tmp %= mod;
        for(int i = 0; i < n; ++ i) xl[S][i] = ksm(tmp, i) * vis[ct[S] & 1];
    }
    for(int S = 0; S < (1 << mid1); ++ S) {
        long long tmp(0);
        for(int i = 0; i < mid1; ++ i) if((S >> i) & 1) tmp += p[i + 1 + mid];
//        printf("S = %d, tmp = %lld\n", S, tmp);
        xr[S].sum = tmp, tmp %= mod;
        for(int i = 0; i < n; ++ i) xr[S][i] = ksm(tmp, i) * vis[ct[S] & 1];
    }
    sort(xl, xl + (1 << mid));
    sort(xr, xr + (1 << mid1));
    int t(0); Tm res(0);
    for(int S = (1 << mid) - 1; S >= 0; -- S) {
        for(; t < (1 << mid1) && (m - n - xr[t].sum - xl[S].sum) >= 0; ++ t) {
            for(i = 0; i < n; ++ i) presum[i] += xr[t][i];
        }
        for(i = 0; i < n; ++ i) {
//            Tm tmp(0);
            for(j = 0; j <= i; ++ j) {
                ans[i] += presum[i - j] * xl[S][j] * C(i, j);
            }
        }
    }
    for(int i = 0; i < n; ++ i) res += ans[i] * rsf[i];
    for(int i = 0; i < n; ++ i) presum[i] = rsf[i] = ans[i] = 0;
    for(int i = 0; i < (1 << mid); ++ i)  {
        xl[i].sum = 0;
        for(int j = 0; j < n; ++ j)
            xl[i][j] = 0;
    }
    for(int i = 0; i < (1 << mid1); ++ i)  {
        xr[i].sum = 0;
        for(int j = 0; j < n; ++ j)
            xr[i][j] = 0;
    }
    for(int i = 0; i < n; ++ i) for(int j = 0; j < n; ++ j) f[i][j] = 0;
    return res.z;

}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, T;
    init(N);
    r1(T);
    while(T --) {
        r1(n);
        m = 0;
        for(i = 1; i <= n; ++ i) r1(p[i]), m += p[i] + 1;
        m = m / 2;
        mm = m % mod;
        if(n == 1) {
            puts("1");
            continue;
        }
        Work1();
        printf("%d\n", Work2());
    }
    return 0;
}
/*
3
1
10
2
3 4
3
3 3 3
*/
```

----

考虑证明一下 $Sperner$ 定理：

根据组合数学，我们考虑引入对称链的概念，也就是对于一条链 $T_1 \subseteq T_2 \subseteq T_3 \subseteq \dots \subseteq T_{n - 1}$ 满足 $|T_i| = |T_{i+ 1}| - 1, |T_1| + |T_{n - 1}| = n$。

那么对于任意一条链都恰好包含一个大小是 $\lfloor \frac{n}{2} \rfloor$ 的子集，显然通过这个可以得出覆盖的数量就是 $\dbinom{n}{\lfloor \dfrac{n}{2} \rfloor}$。

---

我们来证明一下这个的正确性：归纳法

- 对于只有一个元素显然正确。

- 对于加入一个元素 $x$ 的时候，我们考虑已经有的链 $T_1 \subseteq T_2 \subseteq \dots \subseteq T_k$。
  - $k = 1$ 的时候，我们考虑 $T_1 \subseteq T_1 \cup\{x\}$。
  - $k \ne 1$ 的时候，考虑加入链 $T_1 \subseteq T_2 \subseteq \dots \subseteq T_k \cup \{x\}$ 和 $T_1 \cup \{x\}\subseteq T_2 \cup \{x\}\subseteq \dots \subseteq T_{k - 1} \cup \{x\}$。可以保证每个子集都被包含在某个链上

----

然后考虑推广到多重集合的情况：

考虑每次加入 $a$ 个元素 $x$。

对于已有的链 $T_1 \subseteq T_2 \subseteq \dots \subseteq T_k$。

考虑加入链：

- $T_1 \subseteq T_2 \subseteq \dots \subseteq T_k \cup \{x\} \subseteq T_k \cup \{2 \times x\} \subseteq \dots \subseteq T_k \cup \{a \times x\}$
- $T_1 \cup \{x\} \subseteq T_2 \cup \{x\} \subseteq \dots \subseteq T_{k - 1}\cup\{x\}\subseteq T_{k - 1} \cup \{2 \times x\} \subseteq \dots \subseteq T_{k - 1} \cup \{a \times x\}$
- $T_1 \cup \{2 \times x\} \subseteq T_2 \cup \{2 \times x\} \subseteq \dots\subseteq T_{k - 2} \cup \{2 \times x\} \subseteq \dots \subseteq T_{k - 2} \cup \{a \times x\}$
- $\dots$
- $T_1 \cup \{a \times x\} \cup \subseteq T_2 \cup \{a\times x\} \subseteq \dots \subseteq T_{k - a} \cup \{a \times x\}$

显然这个还是链覆盖，而且满足每个子集都被包含在一条链上。

每一条链都包含一个大小为 $\lfloor \dfrac{\sum a_i}{2}\rfloor$ 的子集。也就是说明方案数就是 $\sum_{i} x_i = \lfloor \dfrac{\sum a_i}{2} \rfloor$ 的解的数量。


