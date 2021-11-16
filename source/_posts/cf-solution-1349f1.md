---
title: CF1349F1 Slime and Sequences (Easy Version)
date: 2021-11-16 18:21:00
tags: 
  - 数论
categories:
  - CF题解
---

<h3><center>CF1349F1 Slime and Sequences (Easy Version)</center></h3>

> [CF1349F1 Slime and Sequences (Easy Version)](https://www.luogu.com.cn/problem/CF1349F1)

题目中最明显的限制就是：

- 如果 $i$ 出现，那么必定有 $1 \sim i - 1$ 从小到大出现。

也就是说如果将 $1 \sim i - 1$ 和 $i$ 的位置写出来是一个严格递增的形式。

我们不妨考虑对于每个位置 $i$ 计算数字 $x$ 的答案。

也就是说对于前面的位置我们必须有 $i - 1$ 个上升的数字，来保证其可以出现，当然对于后面都没有影响。

> 具体来说如果后面出现了，我们不计算贡献。
>
> 如果没有出现，显然没算。

我们发现对于一个排列和一个合法的序列是一一对应的，可以考虑对于一个合法的排列，倒着写出 $1$，倒着写出 $2$。

之后有几个上升的肯定就是有多少个合法的当前位置。

后面的位置随便填即可。

答案就是 
$$
ans_x = \sum_{i = 1} ^ n n^{\underline{i}} \times \left<\begin{matrix}i \\ j - 1 \end{matrix} \right>
$$

```cpp
#include <bits/stdc++.h>
using namespace std;

namespace Legendgod {

namespace Read {
//    #define Fread
    #ifdef Fread
    const int Siz = (1 << 21) + 5;
    char buf[Siz], *iS, *iT;
    #define gc() (iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iT == iS ? EOF : *iS ++ ) : *iS ++)
    #define getchar gc
    #endif // Fread

    template <typename T>
    void r1(T &x) {
        x = 0; int f(1); char c(getchar());
        for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
        for(; isdigit(c); c = getchar()) x = (x * 10) + (c ^ 48);
        x *= f;
    }
    template <typename T, typename...Args>
    void r1(T &x, Args&...arg) {
        r1(x), r1(arg...);
    }

}

using namespace Read;

const int maxn = 5e3 + 5;
const int mod = 998244353;
int n;
int fac[maxn], inv[maxn];
int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}
int o[maxn][maxn];
void init(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    inv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) inv[i] = 1ll * inv[i + 1] * (i + 1) % mod;
    for(int i = 1; i <= n; ++ i) {
        o[i][0] = 1;
        for(int j = 1; j <= i; ++ j)
            o[i][j] = (1ll * (i - j) * o[i - 1][j - 1] % mod + 1ll * (j + 1) * o[i - 1][j] % mod) % mod;
    }
}

int ans[maxn];

signed main() {
    int i, j;
    r1(n);
    init(maxn - 5);
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= n; ++ j)
            ans[i] = (ans[i] + 1ll * o[j][i - 1] * fac[n] % mod * inv[j] % mod) % mod;
    }
    for(i = 1; i <= n; ++ i) printf("%d%c", ans[i], " \n"[i == n]);
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }

```

