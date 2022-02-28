---
title: Burnside 定理浅谈
date: 2022-02-28 09:05:20
tags:
  - 数论
categories:
  - 算法
---

首先说一下文章里面木有证明，主要是通过感性理解的方法能更快地了解这个定理。

---

我们设置换群为 $G$。

## 轨道稳定集定理

- $ord(x) = \{g(x), g \in G\}$ 表示元素 $x$ 在置换中得到的所有元素的集合。

- $sta(x) = \{g, g \in G, g(x) = x\}$ 表示对于元素 $x$ 所有将其映射到自身的置换。

我们称 $ord(x)$ 为 $x$ 的轨道，$sta(x)$ 为 $x$ 的稳定集。

那么有定理 $|ord(x)||sta(x)| = |G|$。

## Burnside 定理

我们设

$$
s_g(x) = 
\begin{cases}
1, g(x) = x \\\\\
0, g(x) \ne x
\end{cases}
$$

计数每个 $x$ 不同的轨道数，考虑通过等式进行推出就是：

$$
\begin{aligned}
\sum_{g \in G} \sum_{x \in X} s_g(x) &= \sum_{x \in X} \sum_{g \in G} s_g(x) \\\\
&= \sum_{x \in X} sta(x) \\\\
&= \sum_{x \in X} \frac{|G|}{ord(x)}\\\\
&= |G|\times \sum_{x \in X} \frac{1}{ord(x)} \\\\
&= |G| \times \sum_{A \in X / G} \sum_{x \in A} \frac{1}{|A|}\\\\
&= |G| \times \sum_{A \in X / G} 1\\\\
&= |G| \times |X / G|
\end{aligned}
$$

我们将其带入最初的等式得到：

$$
\begin{aligned}
|X / G| &= \sum_{x \in X} \frac{1}{ord(x)} \\\\
&= \sum_{x \in X} \frac{sta(x)}{|G|} \\\\
&= \frac{1}{|G|} \sum_{x \in X} sta(x) \\\\
&= \frac{1}{|G|} \sum_{g \in G} imm(g)
\end{aligned}
$$

其中 $imm(g)$ 表示置换 $g$ 的不动点个数。

那么这个就是 $Burnside$ 定理。

## Polya 定理

这个其实就是具体到点的置换上，然后不动点的个数是可以直接计算的。

我们不妨考虑对于所有的点都可以染成 $m$ 中颜色。

对于一个循环 $cir(g)$ 在循环节里面的部分显然是可以随便染色的。

那么 $|X / G| = \frac{1}{|G|}\sum_{g \in G} m^{cir(g)}$。

> 这个其实就是该定理。

----

<h2><center>例题分析</center></h2>

[Pólya 定理 - 洛谷](https://www.luogu.com.cn/problem/P4980)

首先发现只有旋转，那么我们构造置换群就是旋转 $0 \to n - 1$ 个的群。

根据 $\tt burnside$ 定理可以计算得到答案：

$$
\sum_{i = 1} ^ n n^{gcd(n, i)}
$$

我们考虑进行莫比乌斯反演得到最终的式子：

$$
\sum_{d | n} n^d \varphi(\frac{n}{d})
$$

时间复杂度 $O(Tn^{\frac{3}{4}})$。

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
const int mod = 1e9 + 7;
int n, m;

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

int phi(int x) {
    int ans(x);
    for(int i = 2; 1ll * i * i <= x; ++ i) if(x % i == 0) {
        ans -= ans / i;
        while(!(x % i)) x /= i;
    }
    if(x != 1) ans -= ans / x;
    return ans;
}

signed main() {
    int i, j, T;
//    printf("%d\n", phi(4));
    r1(T);
    while(T --) {
        r1(n);
        int res(0);
        for(int i = 1; 1ll * i * i <= n; ++ i) if(!(n % i)) {
            res = (res + 1ll * ksm(n, i) * phi(n / i)) % mod;
            if(i * i != n) res = (res + 1ll * ksm(n, n / i) * phi(i)) % mod;
        }
        res = 1ll * res * ksm(n, mod - 2) % mod;
        printf("%d\n", res);
    }
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }
```

[[SHOI2006] 有色图 - 洛谷](https://www.luogu.com.cn/problem/P4128)

考虑通过点置换来计算边，需要使用 $\tt burnside$ 定理。

总共点的置换有 $n!$ 种。

对于完全图我们进行旋转，不妨设点数为 $l$ 那么需要进行分类讨论：

- $\tt l \ is \ odd$，循环就是 $l$，那么贡献的不动点就是总共的边数除以循环，就是 $\frac{l \times (l - 1)}{2l} = \frac{l - 1}{2}$。

- $\tt l \ is\ even$，除了上述的情况，我们还可以考虑图对称的情况，那么也就是旋转 $\frac{l}{2}$，这样当且仅当两点的距离是 $\frac{l}{2}$ 的情况的边才可以满足。也就有 $\frac{\frac{l\times(l - 1)}{2} - \frac{l}{2}}{l} + 1=\frac{l}{2}$。

那么对于同一个循环内的贡献就是 $\lfloor \frac{l}{2} \rfloor$ 。

我们之后考虑不同循环之间的贡献，也就是在 $l1, l2$ 中贡献显然是 $\frac{l1 \times l2}{lcm(l1, l2)} = gcd(l1, l2)$。

我们考虑对于所有置换进行暴力搜索枚举，复杂度就是拆分数。但是我们需要考虑对于这样的置换的总数。

考虑每个置换对于总数的贡献，首先总共点数是有 $n!$，每个置换内部不会产生贡献，相同长度的置换先后顺序没有贡献。

答案就是 $\frac{n!}{\prod b_i \prod l_i!}$。其中 $l_i$ 表示长度为 $i$ 置换的出现次数。

$b_i$ 表示第 $i$ 个置换的长度，考虑内部是一个圆排列，本质就是 $\frac{n!}{b_i!} \times {(b_i - 1)!}$。

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
#define int long long
const int maxn = 2e5 + 5;

int n, m, mod;
int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

int gcd(int a,int b) { return b == 0 ? a : gcd(b, a % b); }

int L[maxn], fac[maxn], ans(0);
int vis[maxn];

void Solve(int x) {
    int res(0), mu(1);
    for(int i = 1; i <= x; ++ i) res = (res + (L[i] >> 1)) % (mod - 1);
    for(int i = 1; i <= x; ++ i) {
        for(int j = i + 1; j <= x; ++ j) {
            res = (res + gcd(L[i], L[j])) % (mod - 1);
        }
    }
    for(int i = 1; i <= x; ++ i) mu = 1ll * mu * L[i] % mod;
    L[0] = 0;
    for(int i = 1; i <= x; ++ i) if(L[i] != L[i - 1]) mu = 1ll * mu * fac[vis[L[i]]] % mod;
    mu = 1ll * fac[n] * ksm(mu, mod - 2) % mod;
    ans = (ans + 1ll * mu * ksm(m, res) % mod) % mod;
}

void dfs(int dep,int left,int last) {
    if(left == 0) Solve(dep - 1);
    if(left < last) return ;
    for(int i = last; i <= left; ++ i) {
        L[dep] = i;
        vis[i] ++;
        dfs(dep + 1, left - i, i);
        -- vis[i];
        L[dep] = 0;
    }
}

signed main() {
    int i, j;
    r1(n, m, mod);
//    assert(mod > (int)(1e9));
    fac[0] = 1;
    for(i = 1; i <= n; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    dfs(1, n, 1);
    ans = 1ll * ans * ksm(fac[n], mod - 2) % mod;
    printf("%lld\n", ans);
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }
```

[[HNOI2009]图的同构计数 - 洛谷](https://www.luogu.com.cn/problem/P4727)

这个题目和上一题本质一样，我们需要考虑图出现还是不出现可以变成染成两种颜色。

> 因为 $n$ 比较大，所以常数需要略微好一点，但是正常都是能过的。

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
#define int long long
const int maxn = 70 + 5;

int n, m, mod;
int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

int gcd1(int a,int b) { return b == 0 ? a : gcd1(b, a % b); }

int Gcd[maxn][maxn];

int gcd(int a,int b) { return Gcd[a][b]; }

int L[maxn], fac[maxn], ans(0);
int vis[maxn];

void Solve(int x) {
    int res(0), mu(1);
    for(int i = 1; i <= x; ++ i) res = (res + (L[i] >> 1));
    for(int i = 1; i <= x; ++ i) {
        for(int j = i + 1; j <= x; ++ j) {
            res = (res + gcd(L[i], L[j]));
        }
    }
    res %= mod - 1;
    for(int i = 1; i <= x; ++ i) mu = 1ll * mu * L[i] % mod;
    L[0] = 0;
    for(int i = 1; i <= x; ++ i) if(L[i] != L[i - 1]) mu = 1ll * mu * fac[vis[L[i]]] % mod;
    mu = 1ll * fac[n] * ksm(mu, mod - 2) % mod;
    ans = (ans + 1ll * mu * ksm(m, res) % mod) % mod;
}

void dfs(int dep,int left,int last) {
    if(left == 0) Solve(dep - 1);
    if(left < last) return ;
    for(int i = last; i <= left; ++ i) {
        L[dep] = i;
        vis[i] ++;
        dfs(dep + 1, left - i, i);
        -- vis[i];
        L[dep] = 0;
    }
}

signed main() {
    int i, j;
    r1(n);
    m = 2, mod = 997;
    for(i = 1; i <= n; ++ i) for(j = i; j <= n; ++ j) Gcd[i][j] = Gcd[j][i] = gcd1(i, j);
//    assert(mod > (int)(1e9));
    fac[0] = 1;
    for(i = 1; i <= n; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    dfs(1, n, 1);
    ans = 1ll * ans * ksm(fac[n], mod - 2) % mod;
    printf("%lld\n", ans);
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }
```

[[SDOI2013]项链 - 洛谷](https://www.luogu.com.cn/problem/P3307)

一个恶心的二合一题目。

我们先计算珠子的数量设其为 $m$。

那么因为有 $3$ 面，我们考虑合法的三种情况 $s_3, s_2, s_1$。

$$
\begin{aligned}
s3 &= \sum_{i = 1} ^ {A} \sum_{j = 1} ^ A \sum_{k = 1} ^ A [gcd(i, j, k) == 1] \\\\
s2 &= \sum_{i = 1} ^ A \sum_{j = 1} ^ A [gcd(i, j) == 1] \\\\
s1 &= 1
\end{aligned}
$$

简单反演就不说了，之后容斥一下，$m = \frac{s_3 + 3s_2 + 2}{6}$。

之后考虑进行计数，但是这里难就在于相邻的两个珠子是不同的。

如果我们知道了 $n$ 个珠子的方案，就是一个 $\tt polya$ 定理。

设其为 $f(n)$。

我们考虑两种情况：

- 这个珠子左右两边的珠子不一样。

- 这个珠子左右两边的珠子一样。

前者我们可以直接进行转移，因为去掉了当前的珠子还是合法的串，也就是 $f(n - 1) \times (m - 2)$。

后者发现去掉了之后是不合法的串，但是如果去掉当前珠子和左右之前任意一个珠子一定是合法的。

> 考虑形式 $axyxaz$ 的形式显然去掉了 $xy$ 都是合法的。

然后既然左右两边都是相同的那么肯定是知道了一个珠子的颜色，就是 $f(n - 2) \times (m - 1)$。

得到转移方程 $f(n) = f(n - 1) \times (m - 2)  + f(n - 2) \times (m - 1)$。

考虑进行推通向。

考虑方程 $x^2 = x(m - 2) + (m - 1)$ 得到 $x = m - 1, - 1$。

得到方程：

$$
\begin{cases}
A_0x_0 + A_1x_1 = f(1) \\\\
A_0x_0^2 + A_1x_1^2 = f(2)
\end{cases}
$$

解得 $f(n) = (m - 1)^n + (-1)^n(m - 1)$。

之后还有一点就是不能直接暴力分解质因数，我们考虑将 $n$ 分解质因子之后同样是可以搜索组合出因子，记得求出 $\varphi$。

$$
\varphi(d) = \prod_{p^c} (p^c - p^{c - 1})
$$

然后注意因为 $n$ 很大可能会被模数整除，我们先让 $n$ 取模模数的平方，之后通过同余方程的性质两边同时除以模数得到答案即可。

需要用到光速乘：

```cpp
int64 Mul(int64 a,int64 b) {
    long double res = (long double) a * b / mod;
    int64 tmp = a * b, t1 = (tmp - (uint64)res * mod + mod) % mod;
    return t1;
}
```

> 本质上就是通过 $\tt long \ double$ 的性质。

这里有个细节就是如果 $mod^2$ 的话 $6$ 的逆元需要用 $\tt exgcd$ 来求。

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

#define int long long
typedef long long int64;
typedef unsigned long long uint64;
const int maxn = 1e7 + 5;
const int64 mod0 = 1e9 + 7, mod1 = mod0 * mod0;
int64 mod = 1e9 + 7;

int64 n, m, A;

bitset<maxn> vis;
int pr[664579 + 5], tot(0), mu[maxn];

void init(int x) {
    vis.reset();
    mu[1] = 1;
    for(int i = 2; i <= x; ++ i) {
        if(!vis[i]) pr[++ tot] = i, mu[i] = - 1;
        for(int64 j = 1, k; k = 1ll * pr[j] * i, k <= x && j <= tot; ++ j) {
            vis[k] = 1;
            if(!(i % pr[j])) break;
            mu[k] = - mu[i];
        }
    }
    for(int i = 1; i <= x; ++ i) mu[i] += mu[i - 1];
}

int pd;

int64 Mul(int64 a,int64 b) {
    long double res = (long double) a * b / mod;
    int64 tmp = a * b, t1 = (tmp - (uint64)res * mod + mod) % mod;
    return t1;
}

int64 ksm(int64 x,int64 mi) {
    mi %= (mod - 1);
    int64 res(1);
    while(mi) {
        if(mi & 1) res = Mul(res, x);
        mi >>= 1;
        x = Mul(x, x);
    }
    return res;
}

void exgcd(int64 a, int64 b, int64 &x,int64 &y) {
    if(b == 0) return x = 1, y = 0, void();
    exgcd(b, a % b, x, y);
    int64 z = y; y = x - a / b * y, x = z;
}

int64 Inv(int64 c, int64 md) {
    int64 x, y;
    exgcd(c, mod, x, y);
    return x;
}

void Solvem() {
    m = 0;
    for(int64 i = 1, last; i <= A; i = last + 1) {
        last = min(A, A / (A / i));
        int64 tmp = A / last, tmp1 = mu[last] - mu[i - 1];
        m = (m + Mul(Mul(Mul(tmp, tmp), tmp), tmp1)) % mod;
        m = (m + Mul(3, Mul(Mul(tmp, tmp), tmp1))) % mod;
    }
    m = (m + 2) % mod;
    int64 iv = Inv(6, mod);
    m = Mul(m, iv);
//    printf("m = %lld\n", m);
    m = (m + mod) % mod;
}

int64 F(int64 n) {
    int64 tmp = ksm(m - 1, n) + (n & 1 ? -1 : 1) * (m - 1);
    tmp = (tmp + mod) % mod;
    return tmp;
}

int up[50], ps[50], ed;

void pre(int64 n) {
    ed = 0;
    for(int i = 1; i <= tot && pr[i] <= n; ++ i) if(n % pr[i] == 0) {
        up[++ ed] = pr[i], ps[ed] = 0;
        while(!(n % pr[i])) ++ ps[ed], n /= pr[i];
    }
    if(n != 1) up[++ ed] = n, ps[ed] = 1;
//    for(int i = 1; i <= ed; ++ i) {
//        printf("%d %d\n", up[i], ps[i]);
//    }
}

int64 ans(0);

void dfs(int dep, int64 d, int64 phi) {
    if(dep == ed + 1) {
        ans = (ans + Mul(phi, F(n / d))) % mod;
        return ;
    }
    dfs(dep + 1, d, phi);
    d *= up[dep], phi *= up[dep] - 1;
    dfs(dep + 1, d, phi);
    for(int i = 2; i <= ps[dep]; ++ i)
        d *= up[dep], phi *= up[dep], dfs(dep + 1, d, phi);
}

signed main() {
//    printf("%.3lf\n", fabs(&edd - &bg) / 1024.0 / 1024.0);
    int i, j, T;
    init(maxn - 5);
//    puts("SSS");
    r1(T);
    while(T --) {
        r1(n, A);
        if(n % mod0 == 0) mod = mod1, pd = 1;
        else mod = mod0, pd = 0;
        Solvem();
        pre(n);
        ans = 0;
        dfs(1, 1, 1);
        if(pd == 0) ans = Mul(ans, ksm(n, mod - 2));
        else ans = Mul(ans / mod0, ksm(n / mod0, mod0 - 2)) % mod0;
        ans = (ans + mod0) % mod0;
        printf("%lld\n", ans);
    }
    return 0;
}
/*
1
2000000014 4
*/
}

signed main() { return Legendgod::main(), 0; }
```
