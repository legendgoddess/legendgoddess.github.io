---
title: CF1523H Hopping Around the Array
date: 2021-08-31 08:40:46
tags:
  - Dp
  - 贪心
categories:
  - CF题解
---

### [CF1523H](https://codeforces.com/problemset/problem/1523/H)

首先这个东西很像一个 $dp$。我们不妨将删点记录成一个状态，然后发现状态数量太多了，我们可以倍增优化一下状态数量。

设 $f(i, j, k)$ 表示从点 $k$ 开始删除了 $j$ 个点，走 $2^i$ 个点能到达的最优的点。

我们考虑最简单的情况，就是从 $i$ 开始走 $j$ 步能走到的最优的点。答案是 $x +a_x$ 最大的点，因为对于一个 $y > x, a_y + y < a_x + x$ 这样就可以走更多的点。

我们通过 $st$ 表的方式直接进行维护即可。

对于一个询问，我们对于二进制进行拆位分析即可，对于一个不能直接到达的加上 $2^i$ 的贡献。注意特判直接能到达的情况和走一步能到达的情况。

> $2^0, 0$。

```cpp
#include <bits/stdc++.h>
using namespace std;

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

//#define int long long
const int maxn = 2e4 + 5;
const int maxm = 2e5 + 5;
const int N = 2e5;

int n, Q;
int st[16][maxm], f[16][31][maxn];
int a[maxm];
int lg[maxm];

int Max(int x,int y) {
    return x + a[x] > y + a[y] ?  x : y;
}

int Ask(int l,int r) {
    int k = lg[r - l + 1];
    return Max(st[k][l], st[k][r - (1 << k) + 1]);
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, Q);
    for(i = 1; i <= n; ++ i) r1(a[i]), (i + a[i] > n ? a[i] = n - i + 1 : 0);
    for(i = 1; i <= n + 1; ++ i) st[0][i] = i;
    a[n + 1] = n + 1;
    for(i = 0; (1 << i + 1) <= n + 1; ++ i) {
        for(j = 1; j + (1 << i + 1) - 1 <= n + 1; ++ j) {
            st[i + 1][j] = Max(st[i][j], st[i][j + (1 << i)]);
        }
    }
    for(i = 0; (1 << i) <= n; ++ i)
        for(j = 0; j <= 30; ++ j)
            f[i][j][n + 1] = n + 1;
    for(i = 1; i <= 15; ++ i) lg[1 << i] = i;
    for(i = 2; i <= N; ++ i) if(!lg[i]) lg[i] = lg[i - 1];
//    puts("ZZZ");
    for(i = 1; i <= n; ++ i) {
        f[0][0][i] = Ask(i + 1, a[i] + i);
        for(j = 1; j <= 30; ++ j) {
            f[0][j][i] = min(n + 1, i + j + a[i]);
        }
    }
    for(i = 1; (1 << i) <= n; ++ i) {
        auto g = f[i], pre = f[i - 1];
        for(j = 0; j <= 30; ++ j) {
            for(int k = 0; k + j <= 30; ++ k) {
                for(int l = 1; l <= n; ++ l) {
                    g[j + k][l] = Max(g[j + k][l], pre[j][pre[k][l]]);
                }
            }
        }
    }
    while(Q --) {
        int l, r, k;
        r1(l, r, k);
        int ans(2);
        if(l == r) { puts("0"); continue; }
        if(l + a[l] + k >= r) { puts("1"); continue; }
        int c = lg[r - l + 1] + 1;
        vector<int> A(k + 3, l);
        while(c --) {
            auto g = f[c]; bool flag(1);
            for(i = 0; i <= k && flag; ++ i) {
                for(j = 0; j + i <= k; ++ j) {
                    int ps = g[i][A[j]];
                    if(ps + a[ps] + k - i - j >= r) {flag = 0; break;}
                }
            }
            if(!flag) continue;
            ans += (1 << c);
            for(int i = k; i >= 0; -- i) {
                for(int j = k - i; j >= 0; -- j) {
                    A[i + j] = Max(A[i + j], g[j][A[i]]);
                }
            }
        }
        printf("%d\n", ans);
    }
	return 0;
}
```




