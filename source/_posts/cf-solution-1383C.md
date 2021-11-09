---
title: CF1383C String Transformation 2
date: 2021-08-08 21:51:02
tags:
  - Dp
  - 图论
categories:
  - CF题解
---

### [CF1383C](https://codeforces.com/contest/1383/problem/C)
观察一下题目的性质，他说了同一种颜色可以有若干个一起变色，那么我们考虑将同一种颜色看成相同的东西。

考虑对于其建图，也就是如果 $A_i$ 变成 $B_i$ 就建立 $A_i \to B_i$ 的边。

然后本质上就是叫我们重新建立一张图满足 $A_i$ 和 $B_i$ 联通，边数最少的图。
> 本质上每一次变换都是需要一次操作。

但是不仅是如此，如果说 $u \to v$  的路径是存在的，必须满足其路径上的边的变化时间是递增的。那么就是让我们对于边进行标号，然后满足图上任意路径都是递增的。

我们发现如果任意两个点都能互相到达，边数最少的就是一个环加上一条链。也就是这样的情况。
![flvHxI.png](https://img-blog.csdnimg.cn/img_convert/ae63ed063a09590996f6278ee933e72c.png)
这样我们需要消耗 $2n - 2$ 条边。

但是我们可以更优，也就是考虑对于这个图，找到一个最大的 $DAG$ 进行向链一样连边，然后后面的点进行这样的操作。

![flxowT.png](https://img-blog.csdnimg.cn/img_convert/2cc324330db83ca9e49f1214c3936f13.png)

这样子肯定是最优的。
然后我们考虑通过 $Dp$ 计算这个答案，发现直接计算选了 $S$ 中的点，最大 $DAG$ 的大小不好算。那么我们就找下面链的最少点个数。

我们对于每一个弱连通块分别考虑，设 $f(S)$ 表示考虑了集合 $S$ 中的点，最少点个数为 $f(S)$。转移的时候看这个点和当前的点是否有连边即可，因为有连边说明会产生环。
> 可能有读者对于这个 $Dp$ 的正确性很疑惑，比如说点 $x$ 都是出边，但是后面才枚举到，这样不是让其变成了环吗？本质上我们枚举了所有点集，从中选出一个最优的点集，所以这种情况会被更优的情况取代，$Dp$ 的正确性从而有保证。

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
const int maxn = 20 + 5;
const int maxm = 1e5 + 5;
int vis[maxn];
int n;
char A[maxm], B[maxm];
int vc1[maxn][maxn];
void add(int u,int v) {
    ++ u, ++ v;
    vc1[u][v] = 1;
}
int vc[maxn][maxn];
int st[maxn], ed(0);
int E[maxn];
void dfs(int p) {
    vis[p] = 1, st[ed ++] = p;
    for(int j = 1; j <= 20; ++ j) if(vc1[p][j] && !vis[j]) dfs(j);
}

#define Online
signed main() {
#ifndef Online
    freopen("S.in", "r", stdin);
    freopen("S.out", "w", stdout);
#endif

    auto calc = [&] (void) {
        const int inf = 0x3f3f3f3f;
        int z = (1 << ed) - 1, i, j;
        for(i = 0; i < ed; ++ i) {
            E[i] = 0;
            for(j = 0; j < ed; ++ j) E[i] |= vc[st[i]][st[j]] << j;
        }
        vector<int> dp(z + 1, inf);
        dp[0] = 0;
        for(int S = 0; S <= z; ++ S) {
            for(int j = 0; j < ed; ++ j) if(!((S >> j) & 1)) {
                dp[S | (1 << j)] = min(dp[S | (1 << j)], dp[S] + !!(S & E[j]));
            }
        }
//        printf("dp[%lld] = %lld\n", ed, dp[z]);
        return dp[z] + ed - 1;
    };

    auto Solve = [&] (void) {
        int i, j;
        memset(vis, 0, sizeof(vis));
        memset(vc, 0, sizeof(vc));
        memset(vc1, 0, sizeof(vc1));
        r1(n);
        scanf("%s%s", A + 1, B + 1);
        for(i = 1; i <= n; ++ i) {
            vc[A[i] - 'a' + 1][B[i] - 'a' + 1] = 1;
            add(A[i] - 'a', B[i] - 'a'), add(B[i] - 'a', A[i] - 'a');
        }
        int ans(0);
        for(i = 1; i <= 20; ++ i) if(!vis[i]) {
            ed = 0;
            dfs(i);
            ans += calc();
//            printf("i = %lld\n", i);
        }
        printf("%d\n", ans);
//        puts("----\n");
    };

    int T;
    r1(T);
    while(T --) Solve();

	return 0;
}
/*
1
3
abc
tsr

3

*/

```


