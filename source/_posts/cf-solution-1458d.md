---
title: CF1458D Flip and Reverse
date: 2021-09-21 20:51:10
tags:
  - 构造
  - 图论
categories:
  - CF题解
---

<h3><center>CF1458D Flip and Reverse</center></h3>

> [CF1458D Flip and Reverse](https://codeforces.com/problemset/problem/1458/D)

题目还是挺清晰的，直接讲思路了。

首先考虑进行 $\tt dp$ 或者贪心，但是直接进行感觉上无法处理上述的限制。

那么我们从限制进行考虑，最好去掉的限制显然就是数字相同，那么如果说进行对于字符串进行前缀和之后两个前缀和相同的位置 $l - 1, r$ 其实对应一个合法的 $[l, r]$ 区间。

之后我们考虑区间翻转和反转正好就是反着遍历这段区间，那么我们对于权值 $s_i \to s_{i + 1}$ 进行连边，那么这个肯定是一个环。我们直接对于已知的每一个环进行贪心即可。

那么本质上就是原串构成的一个合法的欧拉序列。

那么我们只需要求最小的欧拉序即可。

显然因为要让字典序最小那么我们优先放 $0$ 其次放 $1$。

但是对于当前字符的贪心需要证明一个结论：对于原图任意一个欧拉回路可以通过字符串的任意变换得到。

> 首先对于一个欧拉回路，那么肯定不会出现两个环中间连接一条链的情况。肯定是若干个环进行连接，对于两个交错的环我们直接拆开即可。
>
> 或者换一个方法来说，因为一个点变成了环之后可以遍历两次以及以上那么本质上就是若干个环互相嵌套。
>
> 对于不合法的情况也就是没有相邻连接的边。

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

//#define int long long
const int maxn = 5e5 + 5;
const int N = 5e5 + 5;
char s[maxn];
int n, m;

int cnt[maxn * 2][2];

void Solve() {
    int i, j;
    scanf("%s", s + 1);
    n = strlen(s + 1);
    int sum(0);
    for(i = 1; i <= n; ++ i) {
        cnt[sum + N][s[i] - '0'] ++;
        if(s[i] == '0') -- sum; else ++ sum;
    }
    int x(0);
    for(i = 1; i <= n; ++ i) {
        if(cnt[x + N][0] && cnt[x - 1 + N][1]) -- cnt[x + N][0], -- x, putchar('0');
        else if(cnt[x + N][1]) -- cnt[x + N][1], ++ x, putchar('1');
        else -- cnt[x + N][0], -- x, putchar('0');
    }
    puts("");
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int T;
    r1(T);
    while(T --) Solve();
	return 0;
}

```




