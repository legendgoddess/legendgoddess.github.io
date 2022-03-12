---
title: CF1603C Extreme Extension 题解
date: 2022-3-12 18:46:20
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1603C - Codeforces](https://codeforces.com/problemset/problem/1603/C)

首先很容易想到从后向前进行 $\tt Dp$，但是这个复杂度是 $O(n ^ 2)$ 的。

我们考虑我们究竟算的是什么东西，对于当前位置 $y$ 后面的位置为 $x$，如果 $y > x$ 显然要对 $y$ 进行拆分，如何拆分呢？我们肯定需要保证拆出来的若干个数不降，而且最靠左的值是尽量大的。

可以得到我们至少需要拆成 $K$ 个数 $K \ge \lceil \frac{y}{x}\rceil$，那么设最靠左的值为 $b$ 有 $b \le \lfloor\frac{y}{K}\rfloor$。

我们发现这个可以进行数列分块，也就是考虑 $a_i, a_{i + 1}$ 所进行的一种拆分，我们 $a_i$ 后面的值显然是通过 $a_{i + 1}$ 进行拆分得出的，所以我们考虑将 $a_{i + 1}$ 得出的所有拆分计算出来，这个东西是根号级别的，可以通过。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
    namespace Read {
//        #define Fread
        #ifdef Fread
        const int Siz = (1 << 21) + 5;
        char *iS, *iT, buf[Siz];
        #define gc() ( iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iS == iT ? EOF : *iS ++) : *iS ++ )
        #define getchar gc
        #endif
        template <typename T>
        void r1(T &x) {
            x = 0;
            char c(getchar());
            int f(1);
            for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
            for(; isdigit(c); c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
            x *= f;
        }
        template <typename T, typename...Args>
        void r1(T &x, Args&...arg) {
            r1(x), r1(arg...);
        }
        #undef getchar
    }

using namespace Read;

const int maxn = 2e5 + 5;
const int mod = 998244353;

int n, m;
int f[maxn], g[maxn], a[maxn];

void Add(int &u,int c) { ((u += c) >= mod) ? u -= mod : 0; }

void Solve() {
    int i, j, res(0);
    r1(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = n - 1; i >= 1; -- i) {
        Add(f[a[i + 1]], 1);
        for(int j = 1, last; j <= a[i + 1]; j = last + 1) {
            last = a[i + 1] / (a[i + 1] / j);
            int tmp = a[i + 1] / j;
            g[tmp] = f[tmp], f[tmp] = 0;
        }
        for(int j = 1, last; j <= a[i + 1]; j = last + 1) {
            last = a[i + 1] / (a[i + 1] / j);
            int tmp = a[i + 1] / j;
            int K = (a[i] - 1) / tmp + 1;
            Add(f[a[i] / K], g[tmp]);
            Add(res, 1ll * g[tmp] * i % mod * (K - 1) % mod);
        }
    }
    for(int j = 1, last; j <= a[1]; j = last + 1) {
        last = a[1] / (a[1] / j);
        int tmp = a[1] / j;
        f[tmp] = 0;
    }
    printf("%d\n", res);
}

signed main() {
    int i, j, T(1);
    r1(T);
    while(T --) Solve();
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }
```
