---
title: "[USACO21FEB] Counting Graphs P"
date: 2021-08-19 21:00:43
tags:
  - 数论
  - 反演
  - Dp
  - 图论
categories:
  - USACO题解
---

### [P7418 [USACO21FEB] Counting Graphs P](https://www.luogu.com.cn/problem/P7418)

**题目大意：**

给定一张图，对于另一张合法的图，当且仅当两张图 $(u, v)$ 之间所有的路径长度相同。统计所有合法的图的数量。注意路径不一定是简单路径。

看到路径不一定是简单路径基本上就可以猜到做法了。因为是复杂路径一条边重复走是增加 $2$ 的贡献，所以只要任意两点之间最短奇偶路径相同即可。我们可以找出这样的路径。我们任意找一个起点，之后可以得到 $n$ 个数对。我们为了方便钦定 $(x, y), x < y$。

我们考虑一条边可以产生贡献而且是合法的是什么情况。对于一个 $(x, y)$ 来说之前的点对 $ + 1$ 必须保证 $x_1 +1 - x\equiv 0\pmod 2, y$ 同理。然后我们不一定每一个点都要考虑，直接继承就好了。

所以总共有 $3$ 种点。

- ```(x - 1, y - 1)```是 $A$ 类边，点个数为 $a$。
- ```(x - 1, y + 1)```是 $B$ 类边，点个数为 $b$。
- ```(x + 1, y - 1)```是 $C$ 类边，点个数为 $c$。
- ```(x, y)```是当前的点对，点个数为 $d$。

我们发现如果链接了 $A$ 类边直接就合法了。如果不链接 $A$ 类边就必须同时连接 $B, C$ 类边。

我们发现对于当前点，然后对于其左下方的点本质上就是一个子状态。那么我们 $dp$ 的顺序就是让 $x + y$ 小的先 $dp$。不然就是让 $x$ 从小到大 $dp$。

因为需要考虑链接了几个 $B$ 类边。然后我们不可能对于一个点同时计算 $B, C$ 类边。这样就重复计算了。所以不妨钦定只计算 $B$ 类边。同时计算上一个状态的 $C$ 类边。

设 $f(a, b, x)$ 表示当前的点对是 $(a, b)$ 然后恰好还有 $x$ 个点需要链接 $C$ 类边。

之后我们发现进行 $dp$ 的时候需要考虑 $B$ 类边。我们预先设状态

$g(a, b, x)$ 表示对于点对 $(x, y)$ 恰好有 $x$ 个点不链接 $B$ 类边。

我们转移的时候考虑上面一个状态链接了几条边。
$$
g(a, b, x) = \binom{d}{x}\sum_{y = 0} ^ {d} f(a - 1, b + 1, y) \times F(b, y, d - x)
$$
$F(a, b, c)$ 表示有集合 $a$ 和 $c$ 进行连边，要求 $c$ 中每个点都被连到，然后 $a$ 中**恰好** $b$ 个点要连边。

我们考虑通过二项式反演计算这个。可以发现对于 $(2^a - 1) ^b$ 的这种形式，可以当成至少 $|S| - a$ 个点没有连边。

那么我们可以将这个看成**恰好** $a - b$ 个点不能连边。

可以直接计算 $F(a, b, c) = \sum_{i= 0} ^ b (-1)^b\dbinom{b}{i}(2^{a - i} - 1 ^ c)$。

最后我们来计算 $f$。
$$
f(x_1, y_1, x) = \sum_{y = 0} ^ d \binom{d - y}{x} (2^{y} - 1) ^ a g(x_1, y_1, y)
$$
我们考虑一下什么时候统计答案。

发现 $(x, x + 1)$ 这个会和 $(x + 1, x)$ 进行连边，这不就是自环吗？

设 $G(x, y)$ 表示 $x$ 个点内部连边恰好有 $k$ 条边的方案数。

我们还是使用二项式反演可以得到，钦定一下有多少个点度数是 $0$。
$$
G(x, y) = \sum_{i = 0} ^ y (-1)^y \binom{y}{i} 2^{\frac{(x - i + 1) \times (x -i)}{2}}
$$
不妨设这里有 $d$ 个点。那么答案就是 $\sum_{i = 0} ^ {d} G(d, i) f(d, d + 1, y)$。这里需要

如果不是这样的形式的话就是 $f(x, y, 0)$。

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

#define Getmod

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
const int maxn = 2e2 + 5;
const int maxm = maxn << 1;

Tm c[maxn][maxn], pw2[maxn][maxn];
Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res = res * x;
        mi >>= 1;
        x = x * x;
    }
    return res;
}

Tm pw[maxn * maxn];

void init() {
    c[0][0] = 1;
    int i, j;
//    for(j = 0; j < maxn; ++ j) pw2[j][0] = ksm(ksm(2, j) - 1, 0);
    for(i = 0; i < maxn; ++ i) {
        c[i][0] = 1;
        for(j = 1; j < maxn; ++ j)
            c[i][j] = c[i - 1][j - 1] + c[i - 1][j];
//        if(i != 0)
        for(j = 0; j < maxn; ++ j) {
            pw2[i][j] = ksm(ksm(2, i) - 1, j);
        }
    }
    for(i = 0; i < maxn * maxn; ++ i) pw[i] = ksm(2, i);
}

vector<int> vc[maxn];
int n, m;

void add(int u,int v) {
    vc[u].push_back(v);
}

Tm f[maxn][maxn][maxn], g[maxn][maxn][maxn];

int dis[maxn];

int sum[maxn];

struct pii {
    int first, second;
    pii(int a = 0,int b = 0) : first(a), second(b) {}
    int operator < (const pii &b) const {
        if(first + second != b.first + b.second) return first + second < b.first + b.second;
        else return first < b.first;
    }
    int operator == (const pii &b) const {
        return first == b.first && second == b.second;
    }
};
pii a[maxn];

#define mp pii

Tm F(int a,int x,int y) {
    Tm res(0);
    Tm vis[2] = {1, mod - 1};
    for(int i = 0; i <= x; ++ i) res += vis[i & 1] * pw2[a - i][y] * c[x][i]/*, printf("c = %d\n", pw2[a - i][y].z)*/;
//    printf("res = %d, %d %d %d\n", res.z, a, x, y);
    return res;
}



void Solve() {
    int i, j;
    r1(n, m);
    Tm ans(1);
    for(i = 0; i <= n * 2; ++ i) vc[i].clear(), dis[i] = 0x3f3f3f3f;
    for(i = 0; i <= n * 2; ++ i) for(j = 0; j <= 2 * n; ++ j)
        for(int k = 0; k <= n * 2; ++ k) f[i][j][k] = g[i][j][k] = 0;
    for(i = 1; i <= m; ++ i) {
        int u, v;
        r1(u, v);
        add(u, v + n), add(v + n, u);
        add(u + n, v), add(v, u + n);
    }

    static queue<int> q;
    while(!q.empty()) q.pop();
    q.push(1); dis[1] = 0;
    while(!q.empty()) {
        int x = q.front(); q.pop();
        for(i = 0; i < vc[x].size(); ++ i) {
            int to = vc[x][i];
            if(dis[to] > dis[x] + 1) {
                dis[to] = dis[x] + 1;
                q.push(to);
            }
        }
    }

//    puts("-----");

    if(dis[1 + n] == 0x3f3f3f3f) { // 没有奇最短路
//        puts("ZZZ");
        for(i = 0; i <= n; ++ i) sum[i] = 0;
        for(i = 1; i <= n; ++ i) sum[min(dis[i], dis[i + n])] ++;
        for(i = 1; i <= n; ++ i) ans = ans * pw2[sum[i - 1]][sum[i]];
//        for(i = 1; i <= n; ++ i) ans = ans * ksm(ksm(2, sum[i - 1]) - 1, sum[i]);
        printf("%d\n", ans.z);
        return ;
    }

    for(i = 1; i <= n; ++ i) a[i] = mp(min(dis[i], dis[i + n]), max(dis[i], dis[i + n]));
    sort(a + 1, a + n + 1);
    map<pii, int> ct;
    ans = 1;
    for(i = 1; i <= n; i = j + 1) {
        j = i - 1;
        while(j < n && a[j + 1] == a[i]) ++ j;
//        printf("j = %d\n", j);
        ct[a[i]] = j - i + 1;
        int nx = a[i].first, ny = a[i].second;
        int A = ct[mp(nx - 1, ny - 1)], B = ct[mp(nx - 1, ny + 1)], D = j - i + 1;
//        printf("B = %d\n",B);
        Tm vis[2] = {1, mod - 1};
        if(!B) g[nx][ny][(i == 1 ? 0 : D)] = 1;
        else {
            for(int x = 0; x <= D; ++ x) {
                for(int y = 0; y <= B; ++ y)
                    g[nx][ny][x] += f[nx - 1][ny + 1][y] * c[D][x] * F(B, y, D - x);
//                printf("g = %d\n", g[nx][ny][x].z);
            }
        }
//        printf("i1 = %d\n", i);
        for(int x = 0; x <= D; ++ x) {
            for(int y = 0; x + y <= D; ++ y) {
                f[nx][ny][x] += c[D - y][x] * pw2[A][D - x] * g[nx][ny][y];
            }
//            printf("f[%d][%d][%d] = %d\n", nx, ny, x, f[nx][ny][x].z);
        }
        if(j == n || a[j + 1].first != nx + 1 || a[j + 1].second != ny - 1) {
            if(nx + 1 != ny) ans *= f[nx][ny][0];
            else {
//                puts("CCC");
                Tm tmp(0);
                for(int x = 0; x <= D; ++ x) {
                    for(int y = 0; y <= x; ++ y)
                        tmp += vis[y & 1] * c[x][y] * f[nx][ny][x] * pw[(D - y) * (D - y + 1) / 2];                }

//                printf("tmp = %d\n", tmp.z);
                ans *= tmp;
            }
        }
//        printf("ans = %d\n", ans.z);
//        printf("i = %d\n", i);
    }
    printf("%d\n", ans.z);
}

/*
1
4 6
1 2
2 3
3 4
1 3
2 4
1 4
*/

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    init();
    int T;
    r1(T);
    while(T --) Solve();
	return 0;
}
```




