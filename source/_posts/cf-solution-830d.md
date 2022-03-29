---
title: "CF830D Singer House 题解"
date: 2022-3-29 19:49:00
tags:
  - Dp
categories:
  - CF题解
---

[Codeforces - 830D](https://codeforces.com/problemset/problem/830/D)

考虑这个东西可以做到 $O(k^3)$，要么是一个矩阵的形式，要么是二维状态的 $\tt Dp$。

感觉矩阵一下子想不出来有什么前途，考虑对于每个点，其儿子本质上就是子问题，我们考虑设答案为 $f_n$。

考虑转移的时候要么是直接从儿子中转移，要么是考虑儿子的一条路径经过自己得到的，注意两条路径如果要合并的时候是不能有重复节点的。

如果一个节点的**祖先**是自己，那么就存在连边，所以任意一条路径总是可以通过根节点的，我们考虑转移的时候需要通过若干条不相交的路径进行计算，所以设状态为 $f(n, k)$ 表示根节点为 $n$ 选择了 $k$ 条不相交路径的方案。

- 转移的时候考虑根节点自己作为一条路径。

- 两条路径不算根节点。

- 根节点和某条链相连接，可以连接头或者，也就是是否通过**返租边**。

- 根节点合并两条链，总共有 $K + 1$ 条链，选择两条即可，也就是钦定一条头和尾。

$$
\begin{aligned}
f(n, K) &= \sum_{i + j = K - 1} f(n - 1, i) \times f(n - 1, j) \\\\
&+ \sum_{i + j = K} f(n - 1, i) \times f(n - 1, j) \\\\
&+ \sum_{i + j = K} f(n - 1, i) \times f(n - 1, j) \times 2K \\\\
&+ \sum_{i + j = K + 1} f(n - 1, i) \times f(n - 1, j) \times (K + 1) \times K
\end{aligned}
$$

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
namespace Read {
//    #define Fread
    #ifdef Fread
    constexpr int Siz = (1 << 21) + 5;
    char *iS, *iT, buf[Siz];
    #define gc() ( iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++ )
    #define getchar gc
    #endif // Fread
    template <typename T>
    void r1(T &x) {
        x = 0; char c(getchar()); int f(1);
        for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
        for(; isdigit(c); c = getchar()) x = (x * 10) + (c ^ 48);
        x *= f;
    }
    template <typename T, typename...Args>
    void r1(T &x, Args&...arg) {
        r1(x), r1(arg...);
    }
}

using Read::r1;

const int maxn = 4e2 + 5;
const int mod = 1e9 + 7;
int n, m;
int f[maxn][maxn];

void Add(int& c,int x) { c = (c + x) % mod; }
int Mul(int a,int b) { return 1ll * a * b % mod; }

int F(int n,int K) {
    if(!K) return 1;
    if(n == 1) return K == 1;
    if(f[n][K] != -1) return f[n][K];
    int& res = f[n][K]; res = 0;
    for(int i = 0; i <= K; ++ i) {
        Add(res, Mul(F(n - 1, i), F(n - 1, K - i)));
        Add(res, Mul(Mul(F(n - 1, i), F(n - 1, K - i)), 2ll * K % mod));
    }
    for(int i = 0; i < K; ++ i) {
        Add(res, Mul(F(n - 1, i), F(n - 1, K - 1 - i)));
    }
    for(int i = 0; i <= K + 1; ++ i) {
        Add(res, Mul(F(n - 1, i), Mul(F(n - 1, K + 1 - i), 1ll * (K + 1) * K % mod)));
    }
    return res;
}

signed main() {
    int i, j;
    memset(f, -1, sizeof(f));
    r1(n);
    printf("%d\n", F(n, 1));
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }

```
