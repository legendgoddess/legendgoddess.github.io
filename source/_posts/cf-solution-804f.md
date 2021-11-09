---
title: CF804F Fake bullions
date: 2021-09-02 19:01:00
tags:
  - 数论
categories:
  - CF题解
---

### [CF804F Fake bullions](https://codeforces.com/problemset/problem/804/F)

> 说实话这题根据套路应该很容易做出来，但是感觉想要通过思维做出来 $\dots$

一个二合一的题目。

$\tt Part\ 1$

首先考虑题目给定的限制，如果 $u \to v$ 而且 $u$ 的第 $i$ 个人有金条，那么 $v$ 中第 $j$ 个人有假金条当且仅当 $S_ux + i = S_vy + j$ 根据 $\tt Lucas$ 定理可以得到符合条件当且仅当 $\gcd(S_u, S_v) | j - i$。

我们考虑这个的传递性，也就是 $\gcd(\text{path}(u, v)) | j - i$。对于竞赛图我们不妨考虑缩点之后再还原。

因为竞赛图缩点之后的特殊性质，每个点都向后面的所有点连边，所以可以直接根据拓扑序列进行计算。

对于一个点因为同余的性质可以得到 $ans_i = \dfrac{ct_{bel_i}\times S_i}{\gcd(bel_i)}$。

------

$\tt Part\ 2$

之后我们可以求出每个点的最大值和最小值称其为 $mx_i, mn_i$。

对于一个集合 $B$ 最可能合法的情况就是集合 $B$ 中全部是 $mx$，外面全部是 $mn$ 所以我们考虑将集合 $B$ 中全部取 $mx$ 来考虑。

对于整个集合 $A, B$ 最明显的限制就是最小值，那么我们不妨考虑枚举 $B$ 中的最小值不妨设其为 $i$ 。之后找出肯定在 $A$ 中的元素，也就是 $mn_j > mx_i$。

还有可以选择的元素也就是 $mn_j \le mx_i$ 而且 $mx_j > mx_i, mx_j = mx_i \text{ and } i < j$ 本质上就是定义一个偏序关系。

设前面那个是 $ct1$ 后面那个是 $ct2$。

我们直接暴力枚举一下将几个后面的放到前面，之后进行组合数即可。

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

//template <typename T>
//void r1(T &x) {
//	x = 0;
//	char c(getchar());
//	int f(1);
//	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
//	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
//	x *= f;
//}
//
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
const int maxn = 5e3 + 5;
const int maxm = 2e6 + 5;
const int N = 2e6;
int n, A, B;
vector<int> vc[maxn];
void add(int u,int v) {
    vc[u].push_back(v);
}

Tm fac[maxm], inv[maxm];
char s[maxm];

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
    if(a < 0 || b < 0) return 0;
    return fac[a] * inv[b] * inv[a - b];
}

string g[maxn];
int Ln[maxn];
typedef long long ll;
ll mx[maxn], mn[maxn];

int dfn[maxn], st[maxn], ed, low[maxn];
int vis[maxn], tot(0);
int bel[maxn], Siz(0);
int gc[maxn];
vector<int> f[maxn];

int gcd(int a,int b) {
    return b == 0 ? a : gcd(b, a % b);
}

void tarjan(int p) {
    st[++ ed] = p, dfn[p] = low[p] = ++ tot; vis[p] = 1;
    for(int to : vc[p]) {
        if(!dfn[to]) tarjan(to), low[p] = min(low[p], low[to]);
        else if(vis[to]) low[p] = min(low[p], dfn[to]);
    }
    if(low[p] == dfn[p]) {
        bel[p] = ++ Siz; gc[Siz] = Ln[p];
        vis[p] = 0;
        int y;
        while((y = st[ed --]) != p) {
            bel[y] = Siz; gc[Siz] = gcd(gc[Siz], Ln[y]);
            vis[y] = 0;
        }
    }
}


signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    ios::sync_with_stdio(false);
    cin.tie(0);
    int i, j;
    fac[0] = 1;
    for(i = 1; i <= N; ++ i) fac[i] = fac[i - 1] * i;
    inv[N] = ksm(fac[N], mod - 2);
    for(i = N - 1; ~ i; -- i) inv[i] = inv[i + 1] * (i + 1);

//    r1(n, A, B);
    cin >> n >> A >> B;
    for(i = 1; i <= n; ++ i) {
        cin >> (s + 1);
        for(j = 1; j <= n; ++ j) if(s[j] == '1') add(i, j);
    }
    for(i = 1; i <= n; ++ i) {
        cin >> Ln[i] >> g[i];
        for(j = 0; j < Ln[i]; ++ j) g[i][j] ^= 48, mn[i] += g[i][j];
    }
    for(i = 1; i <= n; ++ i) if(!dfn[i]) tarjan(i);
//    for(i = 1; i <= Siz; ++ i) f[i].resize(max(gc[i - 1], gc[i]) + 1);
    for(i = 1; i <= Siz; ++ i) f[i].resize(gc[i] + 1);
    for(i = 1; i <= n; ++ i) for(j = 0; j < Ln[i]; ++ j) {
        f[bel[i]][j % gc[bel[i]]] |= g[i][j];
    }
//    puts("ZZZZ");
    vector<int> ct(Siz + 2, 0);
    for(i = Siz; i > 1; -- i) {
        int gc1 = gcd(gc[i], gc[i - 1]);
        for(int j = 0; j < gc[i]; ++ j) f[i - 1][j % gc1] |= f[i][j], ct[i] += f[i][j];
    }
    for(i = 0; i < gc[1];  ++ i) ct[1] += f[1][i];
    for(i = 1; i <= n; ++ i) mx[i] = 1ll * ct[bel[i]] * Ln[i] / gc[bel[i]];
    Tm res(0);
    for(i = 1; i <= n; ++ i) {
        int ct1(0), ct2(0);
        for(j = 1; j <= n; ++ j) if(i != j) {
            if(mn[j] > mx[i]) ++ ct1; // 一定选
            else if(mx[j] > mx[i] || (mx[j] == mx[i] && j < i)) ++ ct2; // 可能能选
        }
        if(ct1 > A - 1) continue;
        for(j = min(B, min(A - 1 - ct1, ct2)); j + ct1 + 1 >= B; -- j)
            res += C(ct2, j) * C(ct1, B - j - 1);
    }
    cout << res.z << endl;
//    printf("%d\n", res.z);
	return 0;
}
```




