---
title: CF1553I Stairs
date: 2021-08-25 08:06:51
tags:
  - Dp
categories:
  - CF题解
---


### [CF1553I Stairs](http://codeforces.com/problemset/problem/1553/I)
> 说实话网上的题解真的少，这个算是当前最详细的吧。
> 请原谅我参考了别人的代码，还跑得巨慢的这一事。
>
> 不过还是 $O(n^2)$ 过去了，嘻嘻。

有两个思路，是将贡献不同时间计算的结果。

首先一个合法的序列，肯定是将数值连续的一段拿出来，然后可以分成若干段等长的。这个一开始判断一下即可。

之后我们将分段拿出来不妨将其记录为 $s$ 数组。

一个合法的序列就是不能有任意两个 $s$ 构成单调序列。

但是这个需要复杂度较高的 $dp$，我们考虑通过容斥改成钦定有几个数不合法。

对于一个长度 $> 1$ 的段，其有两种单调性。长度为 $1$ 的段，其只有一种单调性，但是向后转移的时候会产生两种单调性。

$\tt Case\ 1$ 

我们将单调性在这个序列转移的时候就计算贡献。

设 $f(i, j, 0/1)$ 表示当前考虑到第 $i$ 个序列，已经钦定了 $j$ 个连续，当前段长度是否是 $1$。注意这个段是表示已经拼接之后的段。
$$
\begin{aligned}
& f(i - 1, j, 0) \to f(i, j + 1, 1) \\
& f(i - 1, j, 1) \times 2 \to f(i, j + 1, 1) \\
& f(i - 1, j, 0) \times 2 \to f(i, j, 0/1) \\
& f(i - 1, j, 1) \to f(i, j, 0/1)
\end{aligned}
$$
前面两个转移表示向后拼接，如果长度不是 $1$ 肯定有两种贡献，如果长度是 $1$，那么我们钦定长度为 $1$ 的贡献肯定是只有一种的，因为我们向后拼接成长度 $> 1$ 的贡献是需要向后转移的时候算的。

对于不钦定的时候，我们转移的接口在长度 $= 1$ 的时候肯定是有两种方案的，然后如果是 $> 1$ 的话，我们要延后贡献所以需要先不计算。

最后计算答案的时候使用容斥即可。

**注意：**

这里顺便说一下我们在长度是 $1$ 向后继承的时候贡献 $\times 2$ 了，这是和后面的最不一样的地方。

这里也导致常数略大，没有卡过去。

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
 
template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}
 
//#define int long long
const int maxn = 1e5 + 5;
const int maxm = maxn << 1;
 
Tm f[2][maxn][2];
int a[maxn], tot(0), s[maxn];
int n;
 
signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= n; i += a[i]) {
        s[++ tot] = a[i];
        for(j = i; j <= i + a[i] - 1; ++ j) if(a[j] != a[i]) return puts("0"), 0;
    }
    if(s[1] != 1) f[1][0][0] = 2;
    else f[1][0][1] = 1;
    for(i = 2; i <= tot; ++ i) {
        int now(i & 1), bef(now ^ 1);
        for(j = 0; j < i; ++ j) {
            f[now][j + 1][0] += f[bef][j][0] + f[bef][j][1] * 2;
            Tm x(i - j), y((f[bef][j][0] + f[bef][j][1]).z);
            if(s[i] != 1) f[now][j][0] += y * 2 * x;
            else f[now][j][1] += y * x;
            f[bef][j][0] = f[bef][j][1] = 0;
        }
//        if(i % 1000 == 0) printf("i = %d\n", i);
    }
    Tm ans(0);
    Tm vis[2] = {1, mod - 1};
    for(i = 0; i <= tot; ++ i) ans += vis[i & 1] * (f[tot & 1][i][0] + f[tot & 1][i][1]);
    printf("%d\n", ans.z);
	return 0;
}
```

---

$\tt Case\ 2$

> 这个是 $CF$ 上高分选手暴力过去的方法。翻了很多在时限边缘的代码 ~~除了 FFT 常数大以外~~，基本上都是这种写法。

还是考虑通过单调性进行 $Dp$。

因为如果长度为 $0$ 的段如果不进行拼接，那么我们会在之后将其当做长度为 $1$ 的段产生贡献，所以我们将长度为 $0$ 的段放到了 $1$ 中看待。

这里我们进行 $dp$ 不是表示拼接之后的长度，而是表示当前段的长度。

也就是 $f(0/1, i)$ 表示当前长度是否为 $1$，钦定了 $i$ 次。

这两个代码最大的不同就是这个只考虑了钦定位置的贡献，上一个是钦定了 $i$ 个，其他位置的贡献同样考虑了。

所以当前 $dp$ 的容斥需要倒着进行，而不是什么二项式反演。

> 虽然感觉上可以这样理解，但是我理解了一个晚上没有什么很好的解释。
>
> 如果转化成至多的话，容斥系数是不对的。
>
> 如果是至少的话，$(-1) ^ k$ 也是不对的。

```cpp
#pragma GCC optimize(2)
#pragma GCC optimize(3)
#pragma GCC diagnostic error "-std=c++11"
#pragma GCC target("avx")
#pragma GCC optimize("Ofast")
#pragma GCC optimize("inline")
#pragma GCC optimize("-fgcse")
#pragma GCC optimize("-fgcse-lm")
#pragma GCC optimize("-fipa-sra")
#pragma GCC optimize("-ftree-pre")
#pragma GCC optimize("-ftree-vrp")
#pragma GCC optimize("-fpeephole2")
#pragma GCC optimize("-ffast-math")
#pragma GCC optimize("-fsched-spec")
#pragma GCC optimize("unroll-loops")
#pragma GCC optimize("-falign-jumps")
#pragma GCC optimize("-falign-loops")
#pragma GCC optimize("-falign-labels")
#pragma GCC optimize("-fdevirtualize")
#pragma GCC optimize("-fcaller-saves")
#pragma GCC optimize("-fcrossjumping")
#pragma GCC optimize("-fthread-jumps")
#pragma GCC optimize("-funroll-loops")
#pragma GCC optimize("-fwhole-program")
#pragma GCC optimize("-freorder-blocks")
#pragma GCC optimize("-fschedule-insns")
#pragma GCC optimize("inline-functions")
#pragma GCC optimize("-ftree-tail-merge")
#pragma GCC optimize("-fschedule-insns2")
#pragma GCC optimize("-fstrict-aliasing")
#pragma GCC optimize("-fstrict-overflow")
#pragma GCC optimize("-falign-functions")
#pragma GCC optimize("-fcse-skip-blocks")
#pragma GCC optimize("-fcse-follow-jumps")
#pragma GCC optimize("-fsched-interblock")
#pragma GCC optimize("-fpartial-inlining")
#pragma GCC optimize("no-stack-protector")
#pragma GCC optimize("-freorder-functions")
#pragma GCC optimize("-findirect-inlining")
#pragma GCC optimize("-fhoist-adjacent-loads")
#pragma GCC optimize("-frerun-cse-after-loop")
#pragma GCC optimize("inline-small-functions")
#pragma GCC optimize("-finline-small-functions")
#pragma GCC optimize("-ftree-switch-conversion")
#pragma GCC optimize("-foptimize-sibling-calls")
#pragma GCC optimize("-fexpensive-optimizations")
#pragma GCC optimize("-funsafe-loop-optimizations")
#pragma GCC optimize("inline-functions-called-once")
#pragma GCC optimize("-fdelete-null-pointer-checks")
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

const int mod = 998244353;

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 1e5 + 5;

int n, a[maxn], s[maxn], tot(0);
int f[2][maxn];

int inc(int x,int y) { return x += y - mod, x + ((x >> 31) & mod); }
void add(int &x,int y) { x = inc(x, y); }
int Mod(int x) { return x -= mod, x + ((x >> 31) & mod); }
signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
//    init(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= n; i += a[i]) {
        s[++ tot] = (a[i] > 1);
        for(j = i; j <= i + a[i] - 1; ++ j) if(a[i] != a[j]) return puts("0"), 0;
    }
    f[s[1]][0] = 1;
    for(i = 1; i < tot; ++ i) {
        if(s[i + 1]) {
            for(j = i - 1; j >= 0; -- j) {
                add(f[1][j + 1], Mod(f[1][j] << 1));
                add(f[1][j + 1], f[0][j]);
                add(f[1][j], f[0][j]);
                f[0][j] = 0;
            }
        }
        else {
            for(j = i - 1; j >= 0; -- j) {
                add(f[0][j + 1], Mod(f[1][j] << 1));
                add(f[0][j + 1], f[0][j]);
                add(f[1][j], f[0][j]);
                f[0][j] = 0;
            }
        }
    }
    long long ans(0), fac(1);
    for(i = 1; i <= tot; ++ i) {
        fac = fac * i % mod;
        long long tmp = (f[0][i - 1] + 2ll * f[1][i - 1]) % mod * fac % mod;
        if((tot - i) & 1) ans = Mod(ans - tmp + mod);
        else ans = Mod(ans + tmp);
    }
    printf("%lld\n", ans);
	return 0;
}
```

[$\tt AClink$](https://codeforces.com/problemset/submission/1553/126921306)


