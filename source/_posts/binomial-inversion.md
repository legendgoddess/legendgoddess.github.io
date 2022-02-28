---
title: 二项式反演
date: 2021-07-23 18:33:44 
tags:
  - 数论
  - 反演
categories:
  - 算法
---
#### 恰好和至多的转换：
$$
\begin{aligned}
&g(m) = \sum_{i = 0}^m \binom{m}{i} f(i) \\\\
&f(m) = \sum_{i = 0} ^ m \binom{m}{i} (-1) ^ {m - i} g(i)
\end{aligned}
$$

$g$ 是至多有几个满足条件，$f$ 是恰好。

这个容斥可以理解成，至多的上限越高那么其方案数肯定是越多的，那么肯定是从多的开始容斥，所以容斥系数是 $(-1)^{m - i}$。

---

#### 恰好和至少的转换：
$$
\begin{aligned}
&g(m) = \sum_{i = m} ^ n \binom{i}{n} f(i) \\\\
&f(m) = \sum_{i = m} ^ n (-1) ^ {i - m}\binom{i}{m} g(i)
\end{aligned}
$$

然后这里我们的 $g$ 是钦定多少一个一定满足条件，$f$ 是恰好。

不要理解成后缀和的意思，因为这个是在集合中计数。

---
$\tt Problem\ 1: \text{[bzoj2839]集合计数}$

一个有 $N$ 个元素的集合有 $2^N$ 个不同子集（包含空集），现在要在这 $2^N$ 个集合中取出若干集合（至少一个），
使得
它们的交集的元素个数为 $m$ ，求取法的方案数，答案模 $1000000007$ 。

容易想到枚举总共又多少个元素是其交集，但是恰好的情况不好计算。

> 不信你试试，我试了半天不会。

然后发现不恰好的情况听好算的，如果说有 $i$ 个元素是交集，那么包含其的集合总共有 $2^{n - i}$ 个，也就是考虑剩下的元素选不选的情况，集合嘛，又不是区间。之后考虑这些集合到底选不选，所以总共的方案数就是 $2^{2^{n - i}}$。

然后考虑容斥得到答案。

$$Ans = f(m) = \sum_{i = m} ^ n (-1) ^{i - m} \binom{i}{m} \binom{n}{i} \times 2^{2^{n - i}}$$

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
const int maxn = 1e6 + 5;
const int maxm = maxn << 1;

const int B = 31623;

Tm pww[B + 5], pwwl[B + 5];

Tm Pw(int x) {
    return pww[x / B] * pwwl[x % B];
}

int n, m;

Tm fac[maxn], inv[maxn];
const int N = 1e6;

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
    return fac[a] * inv[b] * inv[a - b];
}

int pw2[maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    pwwl[0] = 1;
    pw2[0] = 1;
    for(i = 1; i <= N; ++ i) pw2[i] = pw2[i - 1] * 2 % (mod - 1);
    for(i = 1; i <= B; ++ i) pwwl[i] = pwwl[i - 1] * 2;
    pww[0] = 1;
    for(i = 1; i <= B; ++ i) pww[i] = pww[i - 1] * pwwl[B];
    fac[0] = 1;
    for(i = 1; i <= N; ++ i) fac[i] = fac[i - 1] * i;
    inv[N] = ksm(fac[N], mod - 2);
    for(i = N - 1; ~ i; -- i) inv[i] = inv[i + 1] * (i + 1);
//    printf("%d\n", Pw(pw2[3]).z);
    r1(n, m);
    Tm vis[2] = {1, mod - 1}, ans(0);
    for(i = m; i <= n; ++ i)
        ans += vis[(i - m) & 1] * C(i, m) * C(n, i) * (Pw(pw2[n - i]) - Tm(1));
    printf("%d\n", ans.z);
	return 0;
}

```
---

$\tt Problem\ 2:\text{[bzoj3622]已经没有什么好害怕的了}$

给出两个长度均为 $n$ 的序列 $A$ 和 $B$ ，保证这 $2n$ 个数互不相同。现要将 $A$ 序列中的数与 $B$ 序列中的数两两配对，求 $A>B$ 的对数比 $A<B$ 的对数恰好多 $m$ 的配对方案数模 $10^9+9$ 。

首先考虑一下什么时候是合法的，我们不妨设 $A < B$ 总共有 $x$ 对，然后可以得到 $x + x + k = n$ 然后发现 $n - k$ 必须是偶数才有解。

然后解方程得到 $x = \frac{n - k}{2}, x + k = \frac{n + k}{2}$。

然后还是恰好不好算，是真的不好算(可能是我 $Dp$ 不行。

但是我至少可以 $Dp$ 出前 $i$ 个数 $A > B$ 的方案。

我们设 $f(i,j)$ 表示考虑了前 $i$ 个数，有 $j$ 个数是 $A > B$ 的，然后可以得到 $f(i,j) = f(i - 1, j) + f(i  -1, j - 1) \times (cnt_i - (j - 1))$。

后面的 $cnt$ 表示比当前 $A$中数小的，$B$ 中数的个数。

这个直接双指针就好了。

之后反演 $f(m) = \sum_{i = m} ^ n (-1) ^ {i - m} \binom{i}{m} \times f(n, i) \times (n - i)!$

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
const int mod  = 1e9 + 9;
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
const int maxn = 2e3 + 5;
const int maxm = maxn << 1;

Tm fac[maxn], inv[maxn];

const int N = 2e3;

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

int n, m, a[maxn], b[maxn];
Tm cnt[maxn], dp[maxn][maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    fac[0] = 1;
    for(i = 1; i <= N; ++ i) fac[i] = fac[i - 1] * i;
    inv[N] = ksm(fac[N], mod - 2);
    for(i = N - 1; ~ i; -- i) inv[i] = inv[i + 1] * (i + 1);

    r1(n, m);
    if( (n - m) % 2 == 1) return puts("0"), 0;
    m = (n + m) / 2;
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= n; ++ i) r1(b[i]);
    sort(a + 1, a + n + 1), sort(b + 1, b + n + 1);
//    for(i = 1; i <= n; ++ i) printf("%d ", b[i]);
    for(int i = 1, j = 0; i <= n; ++ i) {
//            printf("%d\n", a[i]);
        for(; j < n && b[j + 1] <= a[i]; ++ j) ;
        cnt[i] = j;
    }
//    for(i = 1; i <= n; ++ i) printf("%d " , cnt[i].z);
    for(i = 0; i <= n; ++ i) dp[i][0] = 1;
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= cnt[i].z; ++ j) {
            dp[i][j] = dp[i - 1][j] + dp[i - 1][j - 1] * (cnt[i] - (j - 1));
        } // 钦定
    }

    Tm ans(0), vis[2] = {1, mod - 1};
    for(i = m; i <= n; ++ i) {
        ans += vis[(i - m) & 1] * C(i, m) * dp[n][i] * fac[n - i];
    }
    printf("%d\n", ans.z);
	return 0;
}
/*
4 2
5 35 15 45
40 20 10 30

4
*/

```

---

$\tt Problem\ 3:\text{[bzoj4710]分特产}$

总共有 $n$ 个人，$m$ 种物品，每种物品有 $a_i$ 个，求每个人都有物品的方案数。

首先考虑每个人都有物品不好计算，因为你不知道之前的状态，进行 $Dp$ 会产生后效性。

我们设有 $i$ 个人不被选，其他人的方案任意，可以得到方案数。

$$\prod \binom{a_j - 1 + n - i}{a_j}$$

之后在反演一下就好了。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
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
const int maxn = 2e3 + 5;
const int maxm = maxn << 1;

int n, m;
Tm fac[maxn], inv[maxn];

Tm C(int a,int b) {
    if(a < b) return 0;
    return fac[a] * inv[b] * inv[a - b];
}

const int N = 2e3;

Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}

int a[maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    fac[0] = 1;
    for(i = 1; i <= N; ++ i) fac[i] = fac[i - 1] * i;
    inv[N] = ksm(fac[N], mod - 2);
    for(i = N - 1; ~ i; -- i) inv[i] = inv[i + 1] * (i + 1);

    r1(n, m);
    for(i = 1; i <= m; ++ i) r1(a[i]);
    Tm vis[2] = {1, mod - 1};
    Tm ans(0);
    for(i = 0; i <= n; ++ i) {
        Tm now(1);
        for(j = 1; j <= m; ++ j) {
            now *= C(a[j] - 1 + n - i, a[j]);
        }
        ans += vis[i & 1] * now * C(n, i);
    }
    printf("%d\n", ans.z);

	return 0;
}
/*
5 4
1 3 3 5
*/

```

$\tt Problem\ 3:\text{[Hdu1465]不容易系列}$

> [$link$](https://acm.hdu.edu.cn/showproblem.php?pid=1465)

设容易发现至多 $i$ 个不和法就是 $i!$。

之后直接二项式反演即可。

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

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

#define int long long
const int maxn = 20 + 5;
const int maxm = maxn << 1;

const int N = 20;
int fac[maxn], n, pw[maxn];

void init() {
    int i, j;
    fac[0] = 1;
    for(i = 1; i <= N; ++ i) fac[i] = fac[i - 1] * i;
//    fac[0] = 0;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    init();
    while(scanf("%lld", &n) != EOF) {
        int ans(0);
        pw[n] = 1;
        for(int i = n - 1; i >= 0; -- i) pw[i] = pw[i + 1] * (-1);
        for(int i = 0, c(1); i <= n; ++ i) {
            ans += pw[i] * c * fac[i];
            c = c * (n - i) / (i + 1);
        }
        printf("%lld\n", ans);
    }
	return 0;
}

```


