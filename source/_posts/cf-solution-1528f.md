---
title: CF1528F AmShZ Farm
date: 2021-08-26 21:45:25
tags:
  - 数学
  - 贪心
  - 卷积
categories:
  - CF题解
---

### [CF1528F](https://codeforces.com/problemset/problem/1528/F)

> 其实不难，但是又有懂得都懂的感觉。

>  某谷的翻译真的鬼畜。

**题目大意：**

一个合法的序列 $A$ 是 $\forall j \le n, \sum_{i = 1} ^ n [a_i \ge j] \le n - j + 1$。

给了一个限制 $B$ 序列，要求 $a_{b_1} = a_{b_2} = \dots = a_{b_k}$

求这样 $(A, B)$ 的数量和。

---

首先考虑 $A, B$ 看起来很独立，我们考虑分开计算。

对于 $A$ 的限制我们不妨改变一下。

看成有 $n$ 个人，每个人都选择了一个位置 $a_i$。每个人依次坐其选定的位置，如果这个位置有人了那么就向右坐一个位置。如果 $n + 1$ 这个位置没有人，就是合法的。

我们考虑总方案是 $(n + 1) ^ n$ 然后对于一个合法位置其经过变换可以得到 $n$ 个不合法的位置。

那么合法的方案就是 $(n + 1) ^ {n - 1}$。

然后考虑 $B$ 的计数，因为只要满足 $a_{b_1} = a_{b_2} = \dots = a_{b_k}$ 的限制即可。我们可以任意钦定。

然后我们考虑计算 $B$ 的时候不妨计算所有 $A$ 序列的贡献，之后再 $\div (n + 1)$ 即可。

然后对于一个数相同的情况，总共的数的个数为 $n + 1$ 所以应该外面再乘上 $n + 1$ 正好抵消。

注意 $B$ 选择了相同的位置也不影响。

容易得到方案。
$$
\sum_{i = 0} ^n \binom{n}{i} n^{n - i} i^k
$$

> 稍微解释一下：总共只有 $n$ 个数，去枚举哪些数相同，剩下的数随便选即可。后面就是 $B$ 选择的方案了。

然后和 $n$ 有关就不太行。

直接通过斯特林数化简得到。
$$
\sum_{i = 0} ^ k \begin{Bmatrix}k\\i\end{Bmatrix} \binom{n}{i} i!(n +1) ^ {n - j}
$$
之后发现是一个卷积。

然后斯特林数也是可以使用卷积的，复杂度 $O(k \log k)$。
$$
\begin{Bmatrix}k\\j\end{Bmatrix} = \frac{1}{j !} \sum_{i = 0} ^j \binom{i}{j} (-1) ^j (i - j) ^k
$$
就很一脸卷积样。

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

#ifdef Getmod
const int mod  = 998244353;
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
const Tm G = 3, invG = 332748118;
template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 4e5 + 5;
const int maxm = maxn << 1;

Tm tmp[maxn], Sl[maxn], Sr[maxn], fac[maxn], inv[maxn], C[maxn];
int rev[maxn];
int lim, len;
void getrev(int x) {
    lim = 1, len = 0;
    while(lim <= x) lim <<= 1, ++ len;
    for(int i = 0; i < lim; ++ i) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
}

Tm ksm(Tm x,int mi) {
    if(mi < 0) return 0;
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}

void NTT(Tm *A,int opt = 1) {
    for(int i = 0; i < lim; ++ i) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1; mid < lim; mid <<= 1) {
        Tm wn(ksm((opt == 1) ? G : invG, (mod - 1) / (mid << 1)));
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            Tm W(1);
            for(int k = 0; k < mid; ++ k, W *= wn) {
                Tm x = A[j + k], y = W * A[j + k + mid];
                A[j + k] = x + y;
                A[j + k + mid] = x - y;
            }
        }
    }
    if(opt != 1) {
        Tm z = ksm(lim, mod - 2);
        for(int i = 0; i < lim; ++ i) A[i] *= z;
    }
}

int n, k;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, k);
    Tm vis[2] = {1, mod - 1};
    fac[0] = 1;
    for(i = 1; i <= k; ++ i) fac[i] = fac[i - 1] * i;
    inv[k] = ksm(fac[k], mod - 2);
    for(i = k - 1; ~ i; -- i) inv[i] = inv[i + 1] * (i + 1);
    for(i = 0; i <= k; ++ i) {
        Sl[i] = inv[i] * vis[i & 1];
        Sr[i] = ksm(i, k) * inv[i];
    }
    getrev(2 * k);
//    printf("lim = %d\n", lim);
    NTT(Sl, 1), NTT(Sr, 1);
    for(i = 0; i < lim; ++ i) Sl[i] *= Sr[i];
    NTT(Sl, -1);
//    printf("s1 = %d\n", Sl[k].z);
    Sl[0] = 0;
    C[0] = 1;
    for(i = 1; i <= k; ++ i) C[i] = C[i - 1] * (n - i + 1) * ksm(i, mod - 2);
//    Tm x = ksm(n + 1, n);
//    printf("s0 = %d\n", Sl[0].z);
    Tm ans(0);
    for(i = 0; i <= k; ++ i) ans += C[i] * Sl[i] * fac[i] * ksm(n + 1, n - i);
    printf("%d\n", ans.z);
	return 0;
}

```




