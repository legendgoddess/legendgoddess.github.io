---
title: CF1416B Make Them Equal
date: 2021-09-30 14:07:45
tags:
  - 贪心
categories:
  - CF题解
---

<h3><center>CF1416B Make Them Equal</center></h3>

> [CF1416B Make Them Equal](https://codeforces.ml/problemset/problem/1416/B)

> ~~论我脑子有点问题这一事。~~

发现如果 $i = 1$ 这个肯定是很好做的，我们考虑将每个数都放到位置 $1$ 上，每个数最多被使用 $3$ 次，所以复杂度是 $O(3(n - 1))$。

我们具体来说，对于一个位置 $j$，如果 $j | a_j$ 显然我们直接将其放到位置 $1$ 上即可，不然的话我们考虑将这个位置补齐即可。

我们考虑通过归纳法证明这个事情，如果前 $i$ 个数是合法的，对于第 $i + 1$ 个数，最多需要 $i$ 个单位才能变成整除。而前面位置都是合法的，所以肯定有 $\ge i$ 个单位在 $1$ 上，那么肯定是合法的。

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
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

void Solve() {
    int i, j, n, sum(0);
    r1(n);
    vector<int> a(n + 1, 0);
    for(i = 1; i <= n; ++ i) r1(a[i]), sum += a[i];
    if(sum % n != 0) return puts("-1"), void();
    sum /= n;
    struct Node {
        int i, j, x;
    };
    vector<Node> rs;
    for(i = 2; i <= n; ++ i) {
        int x = a[i] / i;
        if(x * i == a[i]) {
            rs.push_back((Node) {i, 1, x});
            a[i] = 0;
            continue;
        }
        rs.push_back((Node){1, i, i - a[i] % i});
        rs.push_back((Node) {i, 1, x + 1});
        a[i] = 0;
    }
    for(i = 2; i <= n; ++ i) {
        rs.push_back((Node) {1, i, sum - a[i]});
    }
    printf("%u\n", rs.size());
    for(auto v : rs) printf("%d %d %d\n", v.i, v.j, v.x);
//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, a[i]);
 }

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, T;
    r1(T); while(T --) Solve();
	return 0;
}
```




