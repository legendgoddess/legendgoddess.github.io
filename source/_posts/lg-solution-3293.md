---
title: Luogu3293 [SCOI2016]美味
date: 2021-07-29 15:22:49
tags:
  - 主席树
  - 二分
categories:
  - 算法
---
[P3293 [SCOI2016]美味](https://www.luogu.com.cn/problem/P3293)

> 一道好的二分题目。

对于常规的来说肯定是考虑在 $Trie$ 树上找一个最优的点。

但是我们发现这里是 $b_i \ \text{xor}\ (a_j + x)$ 对于 $b, x$ 都是固定的，但是异或却没有分配律，所以就会难搞一点。

我们考虑二分一下答案，和平时的二分答案不一样，我们不能快速地判定可行性，但是我们可以判定对于一位是否可行。

也就是说我们对于每一位分别进行考虑，不妨当前可以得到的前面几位的答案是 $ans$。如果这一位是 $0$，是第 $j$ 位，那么我们就尽量要得到 $1$，所以我们会得到一个合法的选择区间，也就是 $[ans, ans + 2^j - 1]$，如果说这里面有数，那么我们这一位就可以得到 $1$。另一种情况同理。

这样我们就可以转换成查询一个区间中是否有数，直接可持久化线段树就好了。

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

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

struct Node {
    int l, r, siz;
}t[maxn * 20];
const int N = 17;
const int M = 1e5;
int tot(0);
struct Seg {
    #define ls t[p].l
    #define rs t[p].r
    #define mid ((l + r) >> 1)
    void build(int &p,int l,int r) {
        if(!p) p = ++ tot;
        if(l == r) return ;
        build(ls, l, mid), build(rs, mid + 1, r);
    }
    void Insert(int &p,int pre,int l,int r,int pos) {
        if(!p) p = ++ tot, t[p] = t[pre];
        ++ t[p].siz;
        if(l == r) return ;
        if(pos <= mid) ls = 0, Insert(ls, t[pre].l, l, mid, pos);
        else rs = 0, Insert(rs, t[pre].r, mid + 1, r, pos);
    }

    int ask(int p,int pre,int l,int r,int ll,int rr) {
        if(t[p].siz - t[pre].siz <= 0) return 0;
        if(ll <= l && r <= rr) return t[p].siz - t[pre].siz;
        int res(0);
        if(ll <= mid) res += ask(ls, t[pre].l, l, mid, ll, rr);
        if(mid < rr) res += ask(rs, t[pre].r, mid + 1, r, ll, rr);
        return res;
    }

}T;

int rt[maxn], n, m;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    T.build(rt[0], 0, M);
    for(i = 1; i <= n; ++ i) {
        int x; r1(x);
        T.Insert(rt[i], rt[i - 1], 0, M, x);
    }
    for(i = 1; i <= m; ++ i) {
        int b, x, l, r;
        r1(b, x, l, r);
        int L, R, ans(0);
        for(int j = N; ~ j; -- j) {
            int ps = (b >> j) & 1, flag(0);
            if(ps) {
                L = ans;
                R = ans + (1 << j) - 1;
                flag = 0;
            }
            else {
                L = ans + (1 << j);
                R = ans + (1 << j + 1) - 1;
                flag = 1;
            }
            if(!T.ask(rt[r], rt[l - 1], 0, M, max(L - x, 0), min(R - x, M))) flag ^= 1;
            ans |= (flag << j);
        }
        printf("%d\n", ans ^ b);
    }
	return 0;
}

```

