---
title: CF1548C The Three Little Pigs
date: 2021-08-27 08:29:52
tags:
  - 多项式
  - 生成函数
  - 数论
categories:
  - CF题解
---

### [CF1548C The Three Little Pigs](https://codeforces.com/problemset/problem/1548/C)

> 算基础的生成函数题了吧。

> 反正当时隔壁老哥在打 VP 的时候，推了半天 $C$ 没有推出来。被我一眼秒了 $\dots$

就是答案肯定是 $\sum_{i = 0} ^ {n} \binom{3i}{x}$。

发现这个东西就是个二项式定理。

那么如果写成生成函数就是 $[z^x] (1 + x) ^ {3i}$

设 $F(x) = \sum_{i = 0} ^{n} (1 + x) ^ {3i}$ 求通项可以得到。
$$
F(x) = \frac{(1 + x) ^{3n + 3} - (1 + x) ^ 3}{(1 + x)^3 - 1}
$$
上面那个二项式定理展开可以 $O(1)$ 计算每一项。

之后下面那个直接暴力除法就好了。

---

~~大神可以跳过这一段~~

下面那个柿子展开是 $x^3 + 3x^2 + 3x$。

我们考虑对于一个最高次是 $y$ 的多项式，我们除法第一遍做的商肯定是 $y - 3$。

那么得到 $x^y + 3x^{y - 1} + 3x^{y - 2}$。之后这个是要减掉的。

那么本质上就是后面的两项都要被减去这个柿子，当然前面不要忘记乘上 $x^y$ 的系数。

---

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
const int maxn = 3e6 + 5;
const int maxm = maxn << 1;

Tm fac[maxn], inv[maxn];

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

int n, q;

Tm a[maxn];

void init() {
    int i, j;
    fac[0] = 1;
    for(i = 1; i <= 3 * n + 3; ++ i) fac[i] = fac[i - 1] * i;
    inv[3 * n + 3] = ksm(fac[3 * n + 3], mod - 2);
    for(i = 3 * n + 2; ~ i; -- i) inv[i] = inv[i + 1] * (i + 1);
    for(i = 0; i <= 3 * n + 3; ++ i) a[i] = C(3 * n + 3, i);
    a[0] -= 1, a[1] -= 3, a[2] -= 3, a[3] -= 1;
}

Tm f[maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, q);

    init();
//    for(i = 1; i <= 3 * n + 3; ++ i) a[i - 1] = a[i];
    for(i = 3 * n + 3; i > 2; -- i) {
        f[i - 3] = a[i];
        a[i - 1] -= a[i] * 3, a[i - 2] -= a[i] * 3;
    }

    while(q --) {
        int x; r1(x);
        printf("%d\n", f[x].z);
    }

	return 0;
}
```



顺便补充一点，就是只有一个二项式的情况通常是可以求递推公式的。


