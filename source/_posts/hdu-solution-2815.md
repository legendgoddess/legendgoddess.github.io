---
title: Hdu 2815 Mod Tree
date: 2021-08-25 10:41:10
tags:
  - 数论
categories:
  - HDU题解
---

### [Hdu 2815 题解 Mod Tree](https://acm.hdu.edu.cn/showproblem.php?pid=2815)
#### [详解见我的博客](/2021/08/25/exbsgs/)
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


