---
title: CF1278F Cards 加强版
date: 2021-08-26 10:07:53
tags:
  - 数论
  - 反演
categories:
  - CF题解
---

### [P6031 CF1278F Cards 加强版](https://www.luogu.com.cn/problem/P6031)

**题目大意：**

有 $m$ 张牌，其中有一张是王牌。将这些牌均匀随机打乱 $n$ 次，设有 $x$ 次第一张为王牌，求 $x^k$ 的期望值。

答案对 $998244353$ 取模。

---

首先 $x^k$ 不好处理。考虑将其消除掉，会想到使用斯特林数转下降幂之后使用组合数消除掉。

我们考虑枚举几次是王牌。
$$
Ans = \sum_{i = 0} ^ n \frac{1}{m^i} (\frac{m - 1}{m})^{n - i} \binom{n}{i} i^k
$$
不妨设 $p = \frac{1}{m}$。

可以得到 $p^n \sum_{i = 0}^n (\frac{1 - p}{p})^{n - i} \binom{n}{i} i^k$。
$$
\begin{aligned}
&p^n \sum_{i = 0}^n (\frac{1 - p}{p})^{n - i } \binom{n}{i} i^k\\
&p^n \sum_{i = 0}^ n(\frac{1 - p}{p})^{n - i} \binom{n}{i} \sum_{j = 0} ^ k \begin{Bmatrix}k\\j\end{Bmatrix} i^{\underline{j}} \\
&p^n \sum_{j = 0} ^ k \begin{Bmatrix}k\\j\end{Bmatrix}\sum_{i = j} ^ n i^{\underline{j}} \binom{n}{i} (\frac{1}{p} - 1)^{n - i} \\
\end{aligned}
$$
后面的这个很像求导得到的项，我们考虑二项式定理。
$$
(A + x)^n = \sum_{i = 0} ^ n x^i A^{n - i} \binom{n}{i}
$$
经过 $k$ 次求导之后。
$$
(A + x) ^{n -k} n^{\underline{k}} = \sum_{i = k} ^ {n} x^{i - k} A^{n - i} \binom{n}{i} i^{\underline{k}} 
$$
如果取 $x = 1$ 可以得到。
$$
(A + 1) ^ {n -k} n^{\underline{k}} = \sum_{i = k} ^ nA^{n - i} \binom{n}{i} i^{\underline{k}}
$$
所以可以得到：
$$
\begin{aligned}
&p^n \sum_{j = 0} ^ k \begin{Bmatrix}k\\j\end{Bmatrix}\sum_{i = j} ^ n i^{\underline{j}} \binom{n}{i} (\frac{1}{p} - 1)^{n - i} \\
&p^n \sum_{j = 0} ^ k \begin{Bmatrix}k\\j\end{Bmatrix}\frac{1}{p^{n - j}} n^{\underline{j}}\\
&\sum_{i = 0} ^ k \begin{Bmatrix}k\\i\end{Bmatrix} p^in^{\underline{i}}
\end{aligned}
$$
可以直接解决简化版了。

但是因为斯特林数使用多项式也只是 $O(k \log k)$ 的复杂度，是不能通过此题的，考虑将其展开。
$$
\begin{Bmatrix}k\\j\end{Bmatrix} = \frac{1}{j!}\sum_{i = 0} ^ j (-1) ^ {j} \binom{j}{i} (j - i) ^ k
$$
之后带入得到：
$$
\begin{aligned}
&\sum_{i = 0} ^ k p^in^{\underline{i}} \frac{1}{i!}\sum_{j = 0} ^{i} (-1)^j \binom{i}{j} (i - j) ^ k \\
&\sum_{i = 0} ^ k p^i n^{\underline{i}} \frac{1}{i!} \sum_{j = 0} ^ i (-1) ^{i -j} \binom{i}{j} j^k \\
&\sum_{j = 0} ^ kj^k \sum_{i = j} ^ k(-1) ^ {i - j} n^{\underline{i}} p^i \binom{i}{j} \frac{1}{i!} \\
&\sum_{j = 0} ^ k j^k (-1) ^j \sum_{i = j} ^k(-p)^i \binom{n}{i}\binom{i}{j} \\
&\sum_{j = 0} ^ k j^k (-1) ^ j \binom{n}{j} \sum_{i = j} ^ k \binom{n - j}{i - j} (-p) ^ i \\
&\sum_{j = 0} ^ k j^k (-1) ^j \sum_{i = 0} ^ {k - j} \binom{n - j}{i} (-p) ^ {i + j} \\
&\sum_{j = 0} ^ k j^kp^j \sum_{i = 0} ^ {k - j} \binom{n - j}{i} (-p) ^ i
\end{aligned}
$$
根据 $\tt E$$\tt \color{red}T2006$ 神仙的话来说，就是只有一个组合数的情况应该是可以用递推求的。

设：
$$f(x) = \sum_{i = 0} ^ {k - x} \binom{n - x}{i} (-p) ^ i $$。

考虑展开一下二项式。
$$
\begin{aligned}
&\sum_{i = 0} ^ {k - x} \binom{n - x}{i} (-p) ^ i \\
&\sum_{i = 0} ^{k - x} [\binom{n - x - 1}{i} + \binom{n - x - 1}{i - 1}] (-p) ^ i \\
& f(x + 1) + \binom{n - x - 1}{i} (-p) ^ {k - x - 1} + \sum_{i = 0} ^{k - x} \binom{n - x - 1}{i - 1} (-p) ^ i \\
& f(x + 1) + \binom{n - x - 1}{i} (-p) ^ {k - x - 1} + (-p) \sum_{i = 0} ^{k - x - 1} \binom{n - x - 1}{i} (-p) ^ {i} \\
&f(x + 1) + \binom{n - x - 1}{i} (-p) ^ {k - x -1} + (-p) f(x + 1) \\
&(1 - p) f(x + 1) + \binom{n - x - 1}{i} (-p) ^ {k - x - 1}
\end{aligned}
$$
显然 $f(k) = 1$。那么从上向下递推。

我们只需要预处理出 $i^k, \binom{n}{i},(-p) ^ i, (1 -p ) ^ i$ 即可。

如果 $n < k$ 直接跑暴力。

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

bool bg;

const int maxk = 1e7 + 5;

bool vis[maxk];
int pr[maxk / 10];
int tot(0);
int pwxk[maxk], cnx[maxk], inv[maxk], f[maxk], px[maxk], cnxkx[maxk];
int p, n, m, k;
const int mod = 998244353;

int ksm(int x,int mi) {
    int res(1); x %= mod;
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

void ouler(int k) {
    pwxk[1] = 1;
    for(int i = 2; i <= k; ++ i) {
        if(!vis[i]) pr[++ tot] = i, pwxk[i] = ksm(i, k);
        for(int j = 1; j <= tot && i * pr[j] <= k; ++ j) {
            vis[i * pr[j]] = 1;
            pwxk[i * pr[j]] = 1ll * pwxk[i] * pwxk[pr[j]] % mod;
            if(!(i % pr[j])) break;
        } // ok
    }
//    printf("tot = %d\n", tot);
}
void init() {
    ouler(k);
    int i, j;
    cnx[0] = 1, f[0] = 0, px[0] = 1;
    int dp = - p + mod, fc(1);
    for(i = 1; i <= k; ++ i) fc = 1ll * fc * i % mod;
    inv[k] = ksm(fc, mod - 2);
    for(i = k - 1; ~ i; -- i) inv[i] = 1ll * inv[i + 1] * (i + 1) % mod;
    fc = 1;
    for(i = 1; i <= k; ++ i) {
        inv[i] = 1ll * inv[i] * fc % mod;
        fc = 1ll * fc * i % mod;
    }// OK

    for(i = 1; i <= k; ++ i) {
        px[i] = 1ll * px[i - 1] * dp % mod;
        cnx[i] = 1ll * cnx[i - 1] * inv[i] % mod * (n - i + 1) % mod;
    }// OK

    cnxkx[0] = 1ll * cnx[k] * ksm(n, mod - 2) % mod * (n - k) % mod;
    for(int i = 1; i <= k; ++i) cnxkx[i] = 1ll * cnxkx[i - 1] * ksm(n - i, mod - 2) % mod * (k - i + 1) % mod;
    f[k] = 1;
    for(int i = k - 1; ~ i; -- i) f[i] = (1ll * f[i + 1] * (1 + dp) % mod + 1ll * cnxkx[i] * px[k - i] % mod) % mod;
}

int ask() {
    int res(0);
    if(n > k) {
        for(int i = 0, pw(1); i <= k; ++ i, pw = 1ll * pw * p % mod) {
            res = (res + 1ll * pw * pwxk[i] % mod * cnx[i] % mod * f[i] % mod) % mod;
        }
    }
    else {
//        return 0;
//            puts("ZZZ");
        int up(1), invp(ksm(1 - p + mod, mod - 2));
        int dn = ksm(1 - p + mod, n);
        for(int i = 0; i <= k; ++ i) {
            res = (res + 1ll * cnx[i] * up % mod * dn % mod * pwxk[i] % mod) % mod;
//            printf("i = %d, dn = %d, up = %d\n", i, dn, up);
            dn = 1ll * dn * invp % mod;
            up = 1ll * up * p % mod;
        }
//        res = 1ll * res * ksm(p, n) % mod;
    }
    return res;
}

bool ed;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
//    cerr << (&ed - &bg) / 1024.0 / 1024.0 << endl;
    int i, j;
    r1(n, m, k);
    p = ksm(m, mod - 2);
    init();
    if(m == 1) printf("%d\n", ksm(n, k));
    else printf("%d\n", ask());
	return 0;
}
```




