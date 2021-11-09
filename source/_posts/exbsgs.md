---
title: Exbsgs 浅谈
date: 2021-08-25 10:39:13
tags:
  - 数论
  - 分块
categories:
  - 算法
---

$\tt Bsgs$ [例题](https://www.luogu.com.cn/problem/P3846)

> 如果您已经会 $bsgs$ 不妨来看看文末的注意。

求 $a^x \equiv b \pmod p$ 的一个最小正整数解。 $\tt p$ 是素数。

考虑进行分块，设 $m = \lfloor\sqrt p \rfloor$，那么让 $x$ 进行一下拆分得到。

$x = i\times m - j$。

之后对于两部分分别进行计算，放到哈希表中查询即可。

具体来说：

我们先将 $b \times a^j$ 存到 $\tt Hash$ 表中，然后去枚举每一个 $(a^{m})^i$ 去暴力匹配一下即可。

**注意：**

枚举 $i$ 要从 $0$ 开始到 $i$，为了计算是 $0$ 的情况。

特判 $m = 1, b = 1$ 直接返回 $0$ 即可。

```cpp
int ksm(int x,int mi,int p) {
    int res(1);
    if(mi == 0) return res % p;
    while(mi) {
        if(mi & 1) res = res * x % p;
        mi >>= 1;
        x = x * x % p;
    }
    return res;
}
int bsgs(int a,int b,int mod) {
    b %= mod, a %= mod;
    map<int, int> mp; mp.clear();
    int m = sqrt(mod) + 1;
    for(int now(b), i = 0; i < m; ++ i, now = now * a % mod)
        mp[now] = i;
    a = ksm(a, m, mod);
    if(!a) return b == 0 ? 1 : -1;
//    puts("ZZZ");
    for(int now(a), i = 1; i <= m; ++ i, now = now * a % mod) {
//        now = ksm(a, i, mod);
        if(!mp.count(now)) continue;
        int j = mp[now];
        if(i * m - j >= 0) return i * m - j;
    }
    return -1;
}
```

---

$\tt exbsgs$ [例题](https://www.luogu.com.cn/problem/P4195)

$a^x \equiv b \pmod p$  这里 $p$ 不一定是素数。

我们先将 $b, p$ 变成互质，如果说除以了 $ct$ 次公约数，公约数乘积为 $gd$。

那么可以得到 $a^{x - ct} \times \frac{a^{ct}}{gd} \equiv \frac{b}{gd} \pmod p$。

之后为了方便将 $\frac{a^{ct}}{gd}$ 放到右边，之后左边本质上就可以用 $bsgs$ 了。

所以我们需要后面的这个是和 $p$ 互质的。

```cpp
int exgcd(int a,int b,int &x,int &y) {
    if(!b) return x = 1, y = 0, a;
    int z = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return z;
}

int ksm(int x,int mi,int mod) {
    int res(1);
    if(mi == 0) return res % mod;
    while(mi) {
        if(mi & 1) res = res * x % mod;
        mi >>= 1;
        x = x * x % mod;
    }
    return res;
}

int exbsgs(int a,int b,int mod) {
    a %= mod, b %= mod;
    if(b == 1 || mod == 1) return 0;
    map<int ,int> mp; mp.clear();
    int ct(0), x, y, ax(1);
    for(int gd; gd = exgcd(a, mod, x, y), gd != 1; ) {
        if(b % gd) return -1;
        b /= gd, mod /= gd;
        ++ ct;
        ax = ax * (a / gd) % mod;
        if(ax == b) return ct;
    }
    exgcd(ax, mod, x, y);
    int inv = (x % mod + mod) % mod;
    b = b * inv % mod;
    int m = sqrt(mod) + 1;
    for(int i = 0, now(b); i < m; ++ i, now = now * a % mod)
        mp[now] = i;
    a = ksm(a, m, mod);
    if(!a) return (b == 0) ? 1 + ct : -1;
    for(int i = 0, now(1); i <= m; ++ i, now = now * a % mod) {
        if(!mp.count(now)) continue;
        int j = mp[now];
        if(i * m - j >= 0) return i * m - j + ct;
    }
    return -1;
}
```

[$\text{hdu2815 Mod Tree}$](https://acm.hdu.edu.cn/showproblem.php?pid=2815)

这里需要注意一下 $b \ge mod$ 直接无解。

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

#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

int ksm(int x,int mi,int mod) {
	int res(1);
	if(mi == 1) return 1 % mod;
	while(mi) {
		if(mi & 1) res = res * x % mod;
		mi >>= 1;
		x = x * x % mod;
	}
	return res;
}

int exgcd(int a,int b,int &x,int &y) {
	if(!b) return x = 1, y = 0, a;
	int d = exgcd(b, a % b, y, x);
	y -= a / b * x;
	return d;
}

int exbsgs(int a,int b,int mod) {
	static map<int, int> mp; mp.clear();
	a %=  mod, b %= mod;
	if(b == 1 || mod == 1) return 0; // case 1
	int ax(1), ct(0), x, y;
	for(int gd; gd = exgcd(a, mod, x, y), gd != 1; ) {
		if(b % gd) return -1; // case 2
		b /= gd, mod /= gd;
		++ ct;
		ax = ax * (a / gd) % mod;
		if(ax == b) return ct;
	}
	exgcd(ax, mod, x, y), void(); // case 3
	int inv = (x % mod + mod) % mod;
	b = b * inv % mod;
	int m = sqrt(mod) + 1;
	for(int i = 0, now(b); i < m; ++ i, now = now * a % mod) {
		mp[now] = i;
	}
	a = ksm(a, m, mod);
	if(!a) return b == 0 ? 1 + ct : -1;//case 4
	for(int i = 0, now(1); i <= m; ++ i, now = now * a % mod) {
		if(!mp.count(now)) continue;
		int j = mp[now];
		if(i * m - j >= 0) return i * m - j + ct;
	}
	return -1;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int a, b, p;
    while(scanf("%lld%lld%lld", &a, &p, &b) != EOF) {
		if(b >= p) {
			puts("Orz,I can’t find D!");
			continue;
		}
		int ans = exbsgs(a, b, p);
		if(ans == -1) puts("Orz,I can’t find D!");
		else printf("%lld\n", ans);
    }
	return 0;
}
```

**注意：**

- 特判 $b = 1, mod = 1$ 的情况。

- 计算除以 $\gcd$ 的次数的时候，如果已经有 $\frac{a^{ct}}{gd} = \frac{b}{gd}$ 的情况直接返回即可。

- 计算逆元的时候，不要将 $a$ 当做 $ax$。

- 特判 $a = 0$，如果 $b \ne 0$ 就不合法。 


