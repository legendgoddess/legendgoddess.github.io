---
title: CF1391D 505
date: 2021-09-30 15:29:42
tags:
  - Dp
  - 贪心
categories:
  - CF题解
---

<h3><center>CF1391D 505</center></h3>

> [CF1391D 505](https://codeforces.ml/problemset/problem/1391/D)

直接粗略看去没有什么可以入手的地方，考虑什么时候是不合法的。

发现样例给出了 $7, 15$ 的时候是不合法的。

考虑手造几组小样例，发现如果一个大的平方矩阵恰好包含偶数个小的平方矩阵就是不合法的。

考虑 $a^2 = k\times b^2$ 直接暴力带入一下 $b = 2$ 的情况，发现 $a = 4, k = 4$。那么显然对于 $n > 3, m > 3$ 的情况是不合法的。

那么剩下的状态就很少了，选择行列比较短的一个位置进行状压即可，我们只需要状压当前列的状态然后枚举前面状态进行转移即可。

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

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, n, m;
    r1(n, m);
    if(n > 3 && m > 3) return puts("-1"), 0;
    vector<vector<int>> a(n + 2, vector<int>(m + 2, 0));
    for(i = 1; i <= n; ++ i) for(j = 1; j <= m; ++ j) scanf("%1d", &a[i][j]);
    vector<vector<int>> b(min(n, m) + 2, vector<int>(max(n, m) + 2, 0));
    if(n > m)
        for(i = 1; i <= 2; ++ i)
            for(j = 1; j <= m; ++ j)
                b[i][j] = a[j][i];
    if(n > m) swap(n, m);
    const int inf = 0x3f3f3f3f;
    vector<vector<int>> f(m + 2, vector<int>((1 << n) + 5, inf));
    const int z = (1 << n);
    vector<int> ct(z + 5, 0);
    function<int(const int &S, const int &i)> bc;
        bc = [&](const int &S, const int &i) {
            int ct(0);
            for(int j = 1; j <= n; ++ j) {
                int x = (S >> (j - 1)) & 1;
                ct += (x != a[j][i]);
            }
            return ct;
        };
    for(int S = 0; S < z; ++ S) ct[S] = ct[S >> 1] + (i & 1);
    for(int S = 0; S < z; ++ S) f[1][S] = bc(S, 1);
//    printf("%d\n", f[1][3]);
    for(i = 2; i <= m; ++ i) {
        for(int S = 0; S < z; ++ S) {
            auto pd = [&] (const int &a, const int &b) -> bool {
                int ct(0);
                if(n == 1) return true;
                for(int j = 1; j <= 2; ++ j) ct += ((a >> (j - 1)) & 1) + ((b >> (j - 1)) & 1);
                if(!(ct & 1)) return false;
                if(n == 2) return true;
                ct = 0;
                for(int j = 2; j <= 3; ++ j) ct += ((a >> (j - 1)) & 1) + ((b >> (j - 1)) & 1);
                if(!(ct & 1)) return false;
                return true;
            };
            for(int S1 = 0; S1 < z; ++ S1) if(pd(S1, S)) {
//                    puts("YES");
                f[i][S] = min(f[i][S], f[i - 1][S1] + bc(S, i));
            }
//            printf("(%d %d) = %d\n", i, S, f[i][S]);
        }
    }
    int ans(inf);
    for(int S = 0; S < z; ++ S) ans = min(ans, f[m][S]);
    printf("%d\n", ans);
	return 0;
}
/*
2 5
10101
01010
*/
```




