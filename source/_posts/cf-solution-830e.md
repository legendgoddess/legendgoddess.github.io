---
title: CF830E Perpetual Motion Machine
date: 2021-08-07 22:55:17
tags:
  - 构造
  - 图论
  - 数论
categories:
  - CF题解
---

### [CF830E](https://codeforces.com/problemset/problem/830/E)

题目已经很简洁了，我们考虑对于样例进行分析。

- 首先不用脑子就能想到的就是环的情况，有环直接全部填 $1$ 就好了。
> 我指的是环所在的连通块。
- 之后研究样例发现对于树的情况也是有解的，然后我们会想到菊花的情况，对于菊花，也就是 $\exist i, deg_i \ge 4$ 就是有解的，中间填 $2$，周围填 $1$。
- 我们发现 $\forall i, deg_i \le 2$ 是无解的。
- 然后我们发现只有一个 $deg_i = 3$ 的情况不好算，我们先看两个的情况。也就是有两个度数为 $3$ 的点连在一起了，我们直接对于两个点赋值 $2$，其他为 $1$ 即可。
> 一开始我是想着两条链之间的点赋值 $2$，后面发现 $1$ 也是可以的，这样就很简洁了。
---
然后我们开始讨论只有一个 $deg_i = 3$ 的情况。
本质上就是一个点，三条链的情况
![fM23Ae.png](https://img-blog.csdnimg.cn/img_convert/37be51deb74e6cb65da2a3ba88352630.png)
发现我们可以对于每一条链进行单独考虑。我们不妨设这样一条链从头到尾是 $a_1 \sim a_n$ 其中 $a_1$ 是图中的 $1$ 号节点。
之后我们考虑使这个贡献最大，也就是使这个函数最大。
$$- \sum_{i = 1} ^ {n - 1} a_i a_{i + 1} + \sum_{i = 1} ^ n a_i ^ 2$$
然后经典 $\times 2$
$$\dfrac{ \sum_{i = 1} ^ {n - 1} (a_{i + 1} - a_i) ^ 2 + a_1 ^ 2 + a_n ^ 2} {2}$$
然后要让后面的最大，可以用偏微分，然后找找规律得到 $a_i \equiv \frac{1}{n}$。
> 这里本质上是将每个 $a_i$ 看成实数，之后同时乘以 $n$。然后每一个 $a_i$ 都是一个 $\frac{n - i}{n}$ 的形式。
我们要看什么时候这个东西合法，不妨设 $d_i = a_{i} - a_{i -1}, d_1 = a_1$ 那么 $\sum_{i = 1} ^ {n} d_i = a_n = S$。然后 CF 上说最好的选择是 $a_i \equiv \frac{S}{n}$，很好理解就是和固定，每一个数肯定是使其相差要尽量大，就选 $a_i = \frac{Si}{n}$，然后我们考虑反一下这个，因为 $a_1$ 会用到很多次。

然后对于每一条链的赋值我们除了找规律还有另一种方法。

> 我承认我看不懂 CF 题解。

直接对于每一个数进行一次偏微分，然后发现只有一个数其进行偏微分之后存在常数项，然后发现每一个数都是 $\frac{n - i}{n}$。

我们来算一下一条链的贡献。
设三条链分别是 $A, B, C$
然后为了方便我们让 $a_n$ 作为 $1$ 号点。

我们设 $S$ 为 $1$ 号节点的值。

$$2A = \sum_{i = 1} ^ n d_i^2 + S ^ 2,. \sum_{i = 1} ^ n d_i = S,$$

$$2A - S^2 = \frac{S^2}{p}$$

之后我们需要解方程，也就是要保证贡献是负的，我们开始为了方便变号了。不妨考虑设全图的贡献是 $2D$, $- 2D + S^2 = (2A - S^2) +(2B - S^2)+ (2C - S^2)$。长度分别为 $p,  q, z$。

> 全图贡献也是正的。

之后可以得到 $- 2D + S^2 = (2A - S^2) +(2B - S^2)+ (2C - S^2) = (\frac{S^2}{p} + \frac{S^2}{q} + \frac{S^2}{z})$

之后可以解得 $- 2D = S^2\times (\frac{1}{p} + \frac{1}{q} + \frac{1}{z} - 1)$

然后要让 $-2D \le 0$ 也就是合法的，就要 $\frac{1}{p + 1} + \frac{1}{q + 1} + \frac{1}{z + 1}  \le 1$。

> 就是右边要小于 $0$。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
// #define Getmod

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

const int mod  = 1e9 + 7;
#ifdef Getmod
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

// #define int long long
const int maxn = 2e5 + 5;
vector<int> vc[3];
int head[maxn], cnt;
int n, m;
int deg[maxn];
struct Edge {
    int to, next;
}edg[maxn << 1];

void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt, ++ deg[v];
}
int vis[maxn], col[maxn];

void dfs(int p,int pre) {
    vis[p] = 0;
    if(!col[p]) col[p] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        if(vis[to]) dfs(to, p);
    }
}

int getCir(int p,int pre) {
    vis[p] = 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        if(vis[to]) return 1;
        if(getCir(to, p)) return 1;
    }
    return 0;
}

int getdeg(int p,int pre) {
    if(pre && deg[p] == 3) return col[p] = 2, 1;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        if(getdeg(to, p)) return col[p] = 2, 1;
    }
    return 0;
}

void getline(int p,int pre,int opt) {
    vc[opt].push_back(p);
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        getline(to, p, opt);
    }
}

bool cmp(const vector<int> & a, const vector<int> &b) {
    return a.size() < b.size();
}

#define Online
signed main() {
#ifndef Online
    freopen("S.in", "r", stdin);
    freopen("S.out", "w", stdout);
#endif

    auto Print = [&](int opt) {
        if(opt == 0) return puts("NO"), void();
        puts("YES");
        for(int i = 1; i <= n; ++ i) printf("%d%c", col[i], " \n"[i == n]);
        return ;
    };

    auto WorK = [&] () {
        int i, j, flag(0);
        r1(n, m);
        for(i = 0; i < 3; ++ i) vc[i].clear();
        for(i = 1; i <= n; ++ i) vis[i] = head[i] = col[i] = deg[i] = 0; cnt = 0;
        for(i = 1; i <= m; ++ i) {
            int u, v;
            r1(u, v);
            add(u, v), add(v, u);
        }
        for(i = 1; !flag && i <= n; ++ i) if(!vis[i]) {
            if((flag = getCir(i, 0)))  dfs(i, 0);
        }
//        puts("ZZZ");
        for(i = 1; !flag && i <= n; ++ i) if(deg[i] > 3) {
            flag = 1; col[i] = 2, dfs(i, 0);
        }
        for(i = 1; !flag && i <= n; ++ i) if(deg[i] == 3) {
            if((flag = getdeg(i, 0))) {
                col[i] = 2;
                dfs(i, 0);
//                puts("ZZZ");
            }
        }
        for(i = 1; !flag && i <= n; ++ i) if(deg[i] == 3) {
            int tot(0);
            for(int x = 0; x < 3; ++ x) vc[x].clear();
            for(int j = head[i];j;j = edg[j].next) {
                int to = edg[j].to;
                getline(to, i, tot ++);
            }
            sort(vc, vc + 3, cmp);
            if(vc[1].size() == 1 || (vc[1].size() == 2 && vc[0].size() == 1 && vc[2].size() < 5)) continue;
            flag = 1;
            if(vc[2].size() >= 5) {
                col[i] = 6;
                col[vc[0][0]] = 3, col[vc[1][0]] = 4;
                col[vc[1][1]] = 2;
                for(j = 0; j < 5; ++ j) col[vc[2][j]] = 5 - j;
            }
            else {
                col[i] = (vc[0].size() + 1) * (vc[1].size() + 1) * (vc[2].size() + 1);
                for(j = 0; j < 3; ++ j) for(int k = 0; k < vc[j].size(); ++ k)
                    col[vc[j][k]] = col[i] / (vc[j].size() + 1) * (vc[j].size() - k);
            }
        }
        Print(flag);
    };

    int T;
    r1(T);
    while(T --) WorK();
	return 0;
}
/*
3
4 4
1 2
2 3
3 4
1 4

5 4
1 2
1 3
1 4
1 5

9 7
1 3
1 5
2 6
2 7
1 9
9 4
4 2



*/

```






