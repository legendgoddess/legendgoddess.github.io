---
title: CF1344B Monopole Magnets
date: 2021-09-28 15:59:15
tags:
  - 构造
  - 贪心
categories:
  - CF题解
---

<h3><center>CF1344B Monopole Magnets</center></h3>

> [CF1344B Monopole Magnets](https://codeforces.ml/problemset/problem/1344/B)

> 多打 $CF$ 你也会懂一点英语。

------

笔者很菜，入手点是没有找到。就是考虑南磁铁不行的话，我们可以考虑一下两个黑色的格子的关系。也就是说如果这两个在同一个行的话，那么可以考虑到两个格子到当前行的南磁铁路径上肯定都是黑色的。

那么我们考虑是否会存在两个南磁铁的情况，如果存在那么肯定是为了处理有两段黑色的情况，但是下面的南磁铁同样是可以吸引上面的北磁铁，那么这种情况肯定是不行的。

所以可以得出第一个限制 **每行每列最多只有一段黑色**。

之后写了一下，发现过不了样例。样例提示我们这个限制还不够。

一开始想着特判一下 $1 \times n, n \times 1$ 的情况，但是发现对于 $2 \times n$ 的情况，同样会出现类似的不合法的情况。

具体来说就是如果一行是全部白色的，又因为这一行必须要填一个南磁铁。如果对于列来说填的位置是白色，而且这列是有黑色的，那么肯定是不合法的。会导致多刷点。

那么唯一合法的情况就是 **如果存在一行是白色的，至少存在一列是白色的**。

直接考虑这两种情况即可。

对于计算黑色格子的贡献的话，我们考虑对于同一个连通块进行计算，图形肯定是互相有交的若干条线段。肯定存在一条线段的下部分是最低的，那么我们在这个位置放一个北磁铁即可，可以发现其能移动到其他的所有位置。

所以答案就是黑色连通块的数量。

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
const int maxn = 1e3 + 5;
const int maxm = maxn << 1;

char s[maxn][maxn];
int vis[maxn][maxn];
int dis[4][2] = { {0, 1}, {-1, 0}, {0, -1}, {1, 0} };
int n, m;

void dfs(int x,int y) {
    vis[x][y] = 1;
    for(int i = 0; i < 4; ++ i) {
        int nx = x + dis[i][0], ny = y + dis[i][1];
        if(nx < 1 || ny < 1 || nx > n || ny > m) continue;
        if(s[nx][ny] == '#' && !vis[nx][ny]) dfs(nx, ny);
    }
}

int suml[maxn], sumr[maxn];

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) scanf("%s", s[i] + 1);
    int flag(0);
    for(i = 1; i <= n && !flag; ++ i) {
        int pd(0);
        for(j = 1; j <= m && !flag; ++ j) {
            if(s[i][j] == '#' && pd == 0) pd = 1;
            if(s[i][j] == '#' && pd == 2) flag = 1;
            if(s[i][j] == '.' && pd == 1) pd = 2;
        }
    }
//    printf("%d\n", flag);
    for(i = 1; i <= m && !flag; ++ i) {
        int pd(0);
        for(j = 1; j <= n && !flag; ++ j) {
            if(s[j][i] == '#' && pd == 0) pd = 1;
            if(s[j][i] == '#' && pd == 2) flag = 1;
            if(s[j][i] == '.' && pd == 1) pd = 2;
        }
    }

    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= m; ++ j) suml[j] += (s[i][j] == '.');
        for(j = 1; j <= m; ++ j) sumr[i] += (s[i][j] == '.');
    }

    for(i = 1; i <= m && !flag; ++ i) if(suml[i] == n) {
        int pd(0);
        for(j = 1; j <= n && !flag; ++ j) if(sumr[j] == m) pd = 1;
        if(!pd) flag = 1;
    }

    for(i = 1; i <= n && !flag; ++ i) if(sumr[i] == m) {
        int pd(0);
        for(j = 1; j <= m && !flag; ++ j) if(suml[j] == n) pd = 1;
        if(!pd) flag = 1;
    }

//    for(i = 1; i <= n; ++ i) printf("%d : %d\n", i, sumr[i]);

    if(flag) return puts("-1"), 0;

    int ans(0);
    for(i = 1; i <= n; ++ i) for(j = 1; j <= m; ++ j) if(s[i][j] == '#' && !vis[i][j])
        ++ ans, dfs(i, j);
    printf("%d\n", ans);

	return 0;
}
/*

3 3
.#.
###
##.
*/
```




