---
title: CF1644F 题解
date: 2022-3-7 15:37:50
tags:
  - 反演
  - 数论
categories:
  - CF题解
---

发现第一个操作不是很好考虑，我们优先从第二个操作入手。

如果一个数组 $a_1$ 可以通过操作 $2$ 变成数组 $a_2$ **当且仅当** $a_1$ 中每种颜色的下标集合恰好和 $a_2$ 中一种颜色的集合对应。

也就是意味着我们需要的 $a_1$ 其实不一定要考虑颜色，只需要计算出不同的集合的集合的数量即可。

也就是在 $n$ 中选出 $i$ 个集合，本质就是 $\left \{ \begin{matrix}n\\i\end{matrix} \right \}$。

之后我们再考虑操作 $1$，显然我们原来多算了答案，对于 $F(a_1, j), j > 1$ 我们发现每个数组都是可能变成一个新的数组，显然存在 $F(a_1, j) = F(a_1, 2j)$ 的情况，就是说明需要容斥来解决。

时间复杂度是 $\sum_{i = 2} ^ n O(\frac{n}{i} \log \frac{n}{i})$ 我们知道调和级数的复杂度是 $O(n \log n)$ 的，那么后面加一个 $\log$ 就是 $O(n \log ^ 2 n)$，可以通过此题。

---

对于容斥系数的处理，我们可以枚举倍数计算前面的系数。

也可以通过莫比乌斯反演得到：

$$
\begin{aligned}
F(n) &= \sum_{n | d} f(d) \\\\
f(n) &= \sum_{n | d} F(d) \times \mu(\frac{d}{n})
\end{aligned}
$$

我们设 $dp(i, j)$ 表示将原序列分成了 $\lceil \frac{n}{i}\rceil$ 段，分成 $j$ 个集合的方案数。

那么这一部分的贡献就是 $\sum_{j}\mu(i)dp(i, j)$。

直接计算系数：

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
//		#define Fread
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

const int maxn = 2e6 + 5;
const int mod = 998244353;

extern void getrev(int);
extern void Deri(int*, int);
extern void reward(int *, int);
extern int ksm(int, int);
extern void NTT(int, int);
extern void Inv(const int*, int*, int);
extern void Ln(const int*, int*, int);
extern void Mul(const int*, const int*, int*, int, int);
extern void Sqrt(const int*, int*, int);
extern void init(int);
extern void Exp(const int*, int*, int);
extern void Ksm(const int*, int*, int, int, int);

int n, K;

int lim, len, rev[maxn];

int G[maxn], F[maxn], d[maxn];
char str[maxn];
int S[maxn], fac[maxn], finv[maxn], mu[maxn], pr[maxn / 10], tot, vis[maxn];

void GetS(int n) {
    static int tmp1[maxn], tmp2[maxn];
    memset(tmp1, 0, (n + 1) * 8);
    memset(tmp2, 0, (n + 1) * 8);
    for(int i = 0; i <= n; ++ i) {
        int x = (i & 1 ? mod - 1 : 1);
        tmp1[i] = 1ll * x * finv[i] % mod;
        tmp2[i] = 1ll * ksm(i, n) * finv[i] % mod;
    }
    memset(S, 0, (n + 1) * 8);
    Mul(tmp1, tmp2, S, n + 1, n + 1);
}

void init1(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
//    puts("SSS");
    tot = 0;
    mu[1] = 1;
    for(int i = 2; i <= x; ++ i) {
        if(!vis[i]) pr[++ tot] = i, mu[i] = mod - 1;
        for(int j = 1, k; k = 1ll * i * pr[j], k <= x && j <= tot; ++ j) {
            vis[k] = 1;
            if(!(i % pr[j])) break;
            mu[k] = mod - mu[i];
        }
//        printf("%d : %d\n", i, mu[i]);
    }
}

long long prf[maxn], dl[maxn];

signed main() {
    init(20);
//    puts("SSS");
    init1(maxn - 5);
	int i, j;
    r1(n, K);
    GetS(n);
    for(i = 1; i <= n; ++ i) prf[i] = S[i], dl[i] = -1;
    if(min(n, K) > 1) prf[1] = 0;
    for(i = 2; i <= n; ++ i) if(dl[i] != 0) {
        int up = (n + i - 1) / i;
        GetS(up);
        for(j = 2; j <= up; ++ j) {
            prf[j] = (prf[j] + 1ll * dl[i] * S[j]) % mod;
        }
        for(j = 2 * i; j <= n; j += i)
            dl[j] -= dl[i];
    }
    long long ans(0);
    for(i = 1; i <= min(n, K); ++ i) ans = (ans + prf[i] + mod) % mod;
    printf("%lld\n", ans);
	return 0;
}

int inv[maxn], wn[2][20][maxn];

const int G1 = 3, invG1 = ksm(3, mod - 2);

void init(int up) {
    for(int t = 1; t <= up; ++ t) {
        int buf0 = ksm(G1, (mod - 1) / (1 << t));
        int buf1 = ksm(invG1, (mod - 1) / (1 << t));
        wn[0][t][0] = wn[1][t][0] = 1;
        for(int k = 1; k < (1 << t); ++ k) {
            wn[0][t][k] = 1ll * wn[0][t][k - 1] * buf0 % mod;
            wn[1][t][k] = 1ll * wn[1][t][k - 1] * buf1 % mod;
        }
    }
    for(int t = 1; t <= (1 << up); ++ t) inv[t] = ksm(t, mod - 2);
}

void getrev(int x) {
    lim = 1, len = 0;
    while(lim <= x) lim <<= 1, ++ len;
    for(int i = 0; i < lim; ++ i) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
}

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

void NTT(int *A, int opt = 1) {
    for(int i = 0; i < lim; ++ i) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1, ts = 1; mid < lim; mid <<= 1, ++ ts) {
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            const int *w1 = wn[opt == 1 ? 0 : 1][ts];
            for(int k = 0; k < mid; ++ k) {
                int x = A[j + k], y = 1ll * A[j + k + mid] * w1[k] % mod;
                A[j + k] = (x + y) % mod;
                A[j + k + mid] = (x - y + mod) % mod;
            }
        }
    }
    if(opt != 1) {
        int z = inv[lim];
        for(int i = 0; i < lim; ++ i) A[i] = 1ll * A[i] * z % mod;
    }
}

void Inv(const int *F, int *G, int x) { // ПоКэ
    if(x == 1) return G[0] = ksm(F[0], mod - 2), void();
    Inv(F, G, (x + 1) >> 1);
    static int tmpf[maxn];
    getrev(x << 1);
    memset(tmpf, 0, lim * 4);
    for(int i = 0; i < x; ++ i) tmpf[i] = F[i];
    NTT(tmpf), NTT(G);
    for(int i = 0; i < lim; ++ i) G[i] = 1ll * G[i] * (2 - 1ll * tmpf[i] * G[i] % mod + mod) % mod;
    NTT(G, -1);
    for(int i = x; i < lim; ++ i) G[i] = 0;
}

void Deri(int *F, int n) {
    for(int i = 1; i < n; ++ i) F[i - 1] = 1ll * F[i] * i % mod;
    F[n - 1] = 0;
}

void reward(int *F,int n) {
    for(int i = n - 2; i >= 0; -- i) F[i + 1] = 1ll * F[i] * ksm(i + 1, mod - 2) % mod;
    F[0] = 0;
}

void Ln(const int *F,int *G,int n) {
    memset(G, 0, n * 4);
    Inv(F, G, n);
    getrev(n << 1);
    static int tmpf[maxn];
    for(int i = 0; i < n; ++ i) tmpf[i] = F[i];
    fill(tmpf + n, tmpf + lim, 0);
    Deri(tmpf, n);
    NTT(tmpf), NTT(G);
    for(int i = 0; i < lim; ++ i) G[i] = 1ll * G[i] * tmpf[i] % mod;
    NTT(G, -1);
    reward(G, n);
}

int tmpG[maxn];

const int inv2 = ksm(2, mod - 2);

void Mul(const int *A,const int *B,int *ans,int n,int m) {
    static int s1[maxn], s2[maxn];
    getrev(max(n, m) << 1);
    memset(s1, 0, 4 * lim), memcpy(s1, A, n * 4), NTT(s1);
    memset(s2, 0, 4 * lim), memcpy(s2, B, m * 4), NTT(s2);
    for(int i = 0; i < lim; ++ i) ans[i] = 1ll * s1[i] * s2[i] % mod;
    NTT(ans, -1);
}

void Sqrt(const int *F, int *G,int n) {
    if(n == 1) return G[0] = 1, void();
    Sqrt(F, G, (n + 1) >> 1);
    memset(tmpG, 0, n * 4);
    Inv(G, tmpG, n);
    Mul(F, tmpG, tmpG, n, n);
    for(int i = 0; i < n; ++ i) G[i] = 1ll * inv2 * (tmpG[i] + G[i]) % mod;
}

void Exp(const int *F, int *G,int n) {
    if(n == 1) return G[0] = 1, void();
    static int tmp[maxn];
    Exp(F, G, (n + 1) >> 1);
    getrev(n << 1);
    memset(tmp, 0, lim * 4);
    Ln(G, tmp, n);
    for(int i = 0; i < n; ++ i) tmp[i] = (F[i] - tmp[i] + mod) % mod;
    tmp[0] = (tmp[0] + 1) % mod;
    Mul(G, tmp, G, n, n);
    for(int i = n; i < lim; ++ i) G[i] = 0;
}

void Ksm(const int *F, int *G,int n,int miyuan,int mi) {
    memset(G, 0, 4 * n);
    if(mi == 0) return G[0] = 1, void();
    static int tmpf[maxn];
    memset(tmpf, 0, n * 4);
    Ln(F, tmpf, n);
    for(int i = 0; i < n; ++ i) tmpf[i] = 1ll * tmpf[i] * miyuan % mod;
    Exp(tmpf, G, n);
}

}

signed main() { return Legendgod::main(), 0; }

```

反演：

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
//		#define Fread
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

const int maxn = 2e6 + 5;
const int mod = 998244353;

extern void getrev(int);
extern void Deri(int*, int);
extern void reward(int *, int);
extern int ksm(int, int);
extern void NTT(int, int);
extern void Inv(const int*, int*, int);
extern void Ln(const int*, int*, int);
extern void Mul(const int*, const int*, int*, int, int);
extern void Sqrt(const int*, int*, int);
extern void init(int);
extern void Exp(const int*, int*, int);
extern void Ksm(const int*, int*, int, int, int);

int n, K;

int lim, len, rev[maxn];

int G[maxn], F[maxn], d[maxn];
char str[maxn];
int S[maxn], fac[maxn], finv[maxn], mu[maxn], pr[maxn / 10], tot, vis[maxn];

void GetS(int n) {
    static int tmp1[maxn], tmp2[maxn];
    memset(tmp1, 0, (n + 1) * 8);
    memset(tmp2, 0, (n + 1) * 8);
    for(int i = 0; i <= n; ++ i) {
        int x = (i & 1 ? mod - 1 : 1);
        tmp1[i] = 1ll * x * finv[i] % mod;
        tmp2[i] = 1ll * ksm(i, n) * finv[i] % mod;
    }
    memset(S, 0, (n + 1) * 8);
    Mul(tmp1, tmp2, S, n + 1, n + 1);
}

void init1(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
//    puts("SSS");
    tot = 0;
    mu[1] = 1;
    for(int i = 2; i <= x; ++ i) {
        if(!vis[i]) pr[++ tot] = i, mu[i] = mod - 1;
        for(int j = 1, k; k = 1ll * i * pr[j], k <= x && j <= tot; ++ j) {
            vis[k] = 1;
            if(!(i % pr[j])) break;
            mu[k] = mod - mu[i];
        }
//        printf("%d : %d\n", i, mu[i]);
    }
}

long long prf[maxn], f[maxn];

signed main() {
    init(20);
//    puts("SSS");
    init1(maxn - 5);
	int i, j;
    r1(n, K);
    if(min(n, K) == 1) return puts("1"), 0;
    GetS(n);
    long long ans(0);
    for(i = 1; i <= n; ++ i) {
        int up = (n + i - 1) / i;
        GetS(up);
        long long tmp(0);
        for(j = 2; j <= min(up, K); ++ j) tmp = (tmp + S[j]) % mod;
        ans = (ans + 1ll * mu[i] * tmp) % mod;
    }
    printf("%lld\n", ans);
	return 0;
}

int inv[maxn], wn[2][20][maxn];

const int G1 = 3, invG1 = ksm(3, mod - 2);

void init(int up) {
    for(int t = 1; t <= up; ++ t) {
        int buf0 = ksm(G1, (mod - 1) / (1 << t));
        int buf1 = ksm(invG1, (mod - 1) / (1 << t));
        wn[0][t][0] = wn[1][t][0] = 1;
        for(int k = 1; k < (1 << t); ++ k) {
            wn[0][t][k] = 1ll * wn[0][t][k - 1] * buf0 % mod;
            wn[1][t][k] = 1ll * wn[1][t][k - 1] * buf1 % mod;
        }
    }
    for(int t = 1; t <= (1 << up); ++ t) inv[t] = ksm(t, mod - 2);
}

void getrev(int x) {
    lim = 1, len = 0;
    while(lim <= x) lim <<= 1, ++ len;
    for(int i = 0; i < lim; ++ i) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
}

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

void NTT(int *A, int opt = 1) {
    for(int i = 0; i < lim; ++ i) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1, ts = 1; mid < lim; mid <<= 1, ++ ts) {
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            const int *w1 = wn[opt == 1 ? 0 : 1][ts];
            for(int k = 0; k < mid; ++ k) {
                int x = A[j + k], y = 1ll * A[j + k + mid] * w1[k] % mod;
                A[j + k] = (x + y) % mod;
                A[j + k + mid] = (x - y + mod) % mod;
            }
        }
    }
    if(opt != 1) {
        int z = inv[lim];
        for(int i = 0; i < lim; ++ i) A[i] = 1ll * A[i] * z % mod;
    }
}

void Inv(const int *F, int *G, int x) { // ПоКэ
    if(x == 1) return G[0] = ksm(F[0], mod - 2), void();
    Inv(F, G, (x + 1) >> 1);
    static int tmpf[maxn];
    getrev(x << 1);
    memset(tmpf, 0, lim * 4);
    for(int i = 0; i < x; ++ i) tmpf[i] = F[i];
    NTT(tmpf), NTT(G);
    for(int i = 0; i < lim; ++ i) G[i] = 1ll * G[i] * (2 - 1ll * tmpf[i] * G[i] % mod + mod) % mod;
    NTT(G, -1);
    for(int i = x; i < lim; ++ i) G[i] = 0;
}

void Deri(int *F, int n) {
    for(int i = 1; i < n; ++ i) F[i - 1] = 1ll * F[i] * i % mod;
    F[n - 1] = 0;
}

void reward(int *F,int n) {
    for(int i = n - 2; i >= 0; -- i) F[i + 1] = 1ll * F[i] * ksm(i + 1, mod - 2) % mod;
    F[0] = 0;
}

void Ln(const int *F,int *G,int n) {
    memset(G, 0, n * 4);
    Inv(F, G, n);
    getrev(n << 1);
    static int tmpf[maxn];
    for(int i = 0; i < n; ++ i) tmpf[i] = F[i];
    fill(tmpf + n, tmpf + lim, 0);
    Deri(tmpf, n);
    NTT(tmpf), NTT(G);
    for(int i = 0; i < lim; ++ i) G[i] = 1ll * G[i] * tmpf[i] % mod;
    NTT(G, -1);
    reward(G, n);
}

int tmpG[maxn];

const int inv2 = ksm(2, mod - 2);

void Mul(const int *A,const int *B,int *ans,int n,int m) {
    static int s1[maxn], s2[maxn];
    getrev(max(n, m) << 1);
    memset(s1, 0, 4 * lim), memcpy(s1, A, n * 4), NTT(s1);
    memset(s2, 0, 4 * lim), memcpy(s2, B, m * 4), NTT(s2);
    for(int i = 0; i < lim; ++ i) ans[i] = 1ll * s1[i] * s2[i] % mod;
    NTT(ans, -1);
}

void Sqrt(const int *F, int *G,int n) {
    if(n == 1) return G[0] = 1, void();
    Sqrt(F, G, (n + 1) >> 1);
    memset(tmpG, 0, n * 4);
    Inv(G, tmpG, n);
    Mul(F, tmpG, tmpG, n, n);
    for(int i = 0; i < n; ++ i) G[i] = 1ll * inv2 * (tmpG[i] + G[i]) % mod;
}

void Exp(const int *F, int *G,int n) {
    if(n == 1) return G[0] = 1, void();
    static int tmp[maxn];
    Exp(F, G, (n + 1) >> 1);
    getrev(n << 1);
    memset(tmp, 0, lim * 4);
    Ln(G, tmp, n);
    for(int i = 0; i < n; ++ i) tmp[i] = (F[i] - tmp[i] + mod) % mod;
    tmp[0] = (tmp[0] + 1) % mod;
    Mul(G, tmp, G, n, n);
    for(int i = n; i < lim; ++ i) G[i] = 0;
}

void Ksm(const int *F, int *G,int n,int miyuan,int mi) {
    memset(G, 0, 4 * n);
    if(mi == 0) return G[0] = 1, void();
    static int tmpf[maxn];
    memset(tmpf, 0, n * 4);
    Ln(F, tmpf, n);
    for(int i = 0; i < n; ++ i) tmpf[i] = 1ll * tmpf[i] * miyuan % mod;
    Exp(tmpf, G, n);
}

}

signed main() { return Legendgod::main(), 0; }

```
