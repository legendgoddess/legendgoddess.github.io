---
title: "[BZOJ 4973] 比特战争"
date: 2021-09-03 08:16:27
tags:
  - 最小生成树
  - 贪心
categories:
  - BZOJ题解
---

### [bzoj [Lydsy1708月赛]比特战争](https://darkbzoj.tk/problem/4973)

> 其实不是那么想写，但是这题毕竟困扰了我很久，还是动笔了，可能笔者自己也没有很懂，希望大家能谅解一下。本文仅仅是自己的想法罢了。

对于一个连通分量我们可以考虑两种形式也就是要么其自己内部占领，要么是外面来的士兵占领。

如果是外面来的士兵肯定贪心得选取最小的边。

对于最小的边我们不妨考虑一下最小生成树，如果一个连通分量在最终情况下是被外部占领的，那么肯定是选择一个最下的边。如果其不被占领肯定又是一个子问题。

这里通过观察可以得到一个结论，对于最优的答案存在一种构造方案使得对于一个联通块至多只有最小的边被加入。

> 对于一个联通块如果是被别人占领显然是加入最小的边。
>
> 如果其是内部占领之后和其相邻的所有边都不会因为要占领这个联通块而被加入。

我们直接贪心枚举每条边的加入情况即可。

我们不妨考虑从小到大加边，每次更新答案。对于两个联通块其打通的最小花费是 $\max(\max(w, b_v), b_u) \times \min(a_u, a_v)$。我们维护两个值即可。

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

//template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
//    r1(t);  r1(args...);
//}

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

struct Node {
    int u, v, w;
    Node(int x = 0,int y = 0,int z = 2e9) : u(x), v(y), w(z) {}
    int operator < (const Node &z) const {
        return w < z.w;
    }
}E[maxm];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    int n, m;
    struct Union : vector<int>{
        Union(int x = 0) { this->resize(x + 1); }
        int getfa(int x) {
            return x == this->at(x) ? x : this->at(x) = getfa(this->at(x));
        }
    };
    r1(n), r1(m);
    Union T(n);
    for(i = 1; i <= n; ++ i) T[i] = i;

    vector<int> a(n + 1, 0), b(n + 1, 0);
    vector<long long> val(n + 1, 0);
    vector<int> mn(n + 1, 2e9), mx(n + 1, 0);
    long long ans(1e18), sum(0);
    for(i = 1; i <= n; ++ i) r1(a[i]), r1(b[i]), val[i] = 1ll * a[i] * b[i], sum += val[i], mn[i] = b[i], mx[i] = a[i];
    for(i = 0; i < m; ++ i) {
        r1(E[i].u), r1(E[i].v), r1(E[i].w);
    }
    sort(E, E + m);
//    puts("ZZZ");
    for(int i = 0; i < m; ++ i) {
        int x(E[i].u), y(E[i].v), w(E[i].w);
        x = T.getfa(x), y = T.getfa(y);
        if(x == y) continue;
        T[y] = x;
        mx[x] = max(mx[x], max(mx[y], w));
        mn[x] = min(mn[x], mn[y]);
        long long tmp1 = val[x] + val[y];
        tmp1 = min(tmp1, 1ll * mx[x] * mn[x]);
        sum -= val[x] + val[y];
        val[x] = tmp1;
        sum += val[x];
        ans = min(ans, sum);
    }
    printf("%lld\n", ans);
	return 0;
}
```




