---
title: "「EZEC-7」猜排列"
date: 2021-09-10 15:13:17
tags:
  - 数论
  - Dp
  - 分治
categories:
  - 洛谷题解
---

### [P7444 「EZEC-7」猜排列](https://www.luogu.com.cn/problem/P7444)

> 这个是 $\textbf{2021.9.10}$ 未完成的最后一题。

就是先找找性质，发现 $f(0)$ 的时候本质上就是不包含 $0$ 的区间个数，不妨设 $0$ 在位置 $i$ 那么显然 $(i - 1) \times (n - i) = f(0)$。最多有两个这样的位置。

> 而且是对称的。

之后我们考虑进行 $\tt Dp$ 每次记录极大的区间也就是设 $f(i, l, r)$ 表示考虑了 $1 \sim i$ 的位置，极大区间是 $[l, r]$ 的方案数。

然后对于一个位置 $x$ 放在 $l$ 的左边的时候，可以满足 $c_i = (l - x + 1) \times (n - r + 1)$。

为了找到 $x$ 的位置显然 $n - r + 1 | c_i$。

如果出现 $c_i = 0$ 的情况，那么这个位置肯定只能放在 $[l, r]$ 中间，那么我们看一下是否能放即可。

直接讲一下优化，就是将之前的东西打表发现每一个 $dp$ 只有一个 $l$ 是有用的。我们不妨每次记录之前有用的 $l$ 然后用另外一个数组记录 $r$ 进行更新即可。

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

//#define int long long
const int maxn = 5e5 + 5;
const int maxm = maxn << 1;

Tm f[2][maxn];
int st[2][maxn], n;
typedef long long ll;
ll c[maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) r1(c[i - 1]);
    auto Calc = [&] (ll x) -> ll{
        return x * (x + 1) >> 1;
    };
    vector<int> ps;
    for(i = 1; i <= n; ++ i) {
        if(Calc(i - 1) + Calc(n - i) == c[0]) ps.push_back(i);
    }
    if(ps.empty()) return puts("0"), 0;
    f[0][ps[0]] = 1, st[0][ps[0]] = ps[0];
    vector<ll> pre, nxt;
    pre.push_back(ps[0]);

    vector<int>  vis(n + 2, 0);

    for(i = 1; i < n - 1; ++ i) {
        nxt.clear();
        int bef((i - 1) & 1), now(i & 1);
        for(auto v : pre) {
            int l = v, r = st[bef][l];
            if(vis[l]) continue;
            vis[l] = 1;
            if(!c[i]) {
                if(r - l + 1 - i > 0) { // 只能放中间
                    f[now][l] += f[bef][l] * (r - l + 1 - i);
                    st[now][l] = r;
                    nxt.push_back(l);
                }
            }
            else {
                ll L(l), R(n - r + 1);
                if(c[i] % R == 0) {
                    ll nl = l - c[i] / R;
                    f[now][nl] += f[bef][l];
                    st[now][nl] = r;
                    nxt.push_back(nl);
                }
                if(c[i] % L == 0) {
                    ll nr = r + c[i] / L;
                    f[now][l] += f[bef][l];
                    st[now][l] = nr;
                    nxt.push_back(l);
                }
            }
        }
        for(auto v : pre) vis[v] = 0, f[bef][v] = 0;
        nxt.swap(pre);
    }
    Tm ans(0);
    for(i = 1; i <= n; ++ i) ans += f[(n - 2) & 1][i];
    ans *= Tm(ps.size());
    printf("%d\n", ans.z);
	return 0;
}

```




