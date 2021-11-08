---
title: [六省联考2017] 分手是祝愿
date: 2021-10-07 10:48:41
tags: 
  - Dp
categories: 
  - 省选题解
---

<h3><center>[六省联考2017]分手是祝愿</center></h3>

> [[六省联考2017]分手是祝愿](https://www.luogu.com.cn/problem/P3750)

对于每一种情况，我们肯定是从大到小依次进行操作，那么对于一次操作是不能用其他操作进行代替的。

> 如果可以代替要么代替的那个变得不合法，要么其本身就是不用进行操作的。

所以我们可以直接计算出来原来序列需要进行多少次方案。

---

根据期望的线性性，而且现在本质上只和操作正确的次数有关，我们直接对于有几个还要操作进行 $\tt Dp$。

设 $f(i)$ 表示从还有 $i$ 个需要进行操作到 $i - 1$ 个需要的期望步数。
$$
f(i) = \frac{i}{n} + \frac{n - i}{n} \times (f(i + 1) + 1) \\
f(n) = 1
$$
然后题目的限制得到 $f(i) = 1, i \le K$。

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

#define int long long
const int maxn = 1e5 + 5;
const int maxm = maxn << 1;
const int mod = 100003;

int n, K;
int a[maxn], f[maxn], inv[maxn];
int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, sum(0);
    r1(n, K);
    for(i = 1; i <= n; ++ i) r1(a[i]), inv[i] = ksm(i, mod - 2);
//    printf("%lld %lld\n", inv[4], inv[4] * 4);
    for(i = n; i >= 1; -- i) if(a[i]) {
        ++ sum;
        for(int j = 1; j * j <= i; ++ j) if(i % j == 0) {
            a[j] ^= 1;
            if(j * j != i) a[i / j] ^= 1;
        }
    }
//    printf("sum = %lld\n", sum);
    int ans(0);
    if(sum <= K) ans = sum % mod;
    else {
        f[n] = 1;
        for(i = n - 1; i > K; -- i) f[i] = (1ll * (n - i) * (f[i + 1] + 1) % mod * inv[i] % mod + 1) % mod;
        for(i = K; i >= 1; -- i) f[i] = 1;
        for(i = 1; i <= sum; ++ i) ans = (ans + f[i]) % mod;
    }
    for(i = 1; i <= n; ++ i) ans = 1ll * ans * i % mod;
    printf("%lld\n", ans);
	return 0;
}
/*
4 0
0 0 1 1
*/

```




