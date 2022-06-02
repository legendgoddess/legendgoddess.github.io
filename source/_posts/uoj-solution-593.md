---
title: "#593. 新年的军队 题解"
date: 2022-6-2 7:50:00
tags:
  - 生成函数
  - 概率论
categories:
  - UOJ题解
---

## [#593. 新年的军队](https://uoj.ac/problem/593)

属实是一道神仙题，估计是去年这个时候听说了这道题，最近把这个坑填了。

给后面要来写的人提个醒，这个题其实没有想象地那么恐怖，代码其实也不复杂，只是推导十分困难。

我说我这篇是全网最详细的不过分吧。

---

### 暴力

首先我们熟悉一下题目，本质上就是考虑所有的排列 $p$，一个合法的排列有 $m$ 个 $p_i > p_{i + 1}$，求 $\forall l, p_k = l$ 的排列 $p$ 的方案数。

暴力直接全排列即可。

----

### 转化

显然是人都会全排列，我们考虑点别的。可以发现这个 `恰好 m 个下降` 就是欧拉数。

我们现在的困难就是如何对应每个 $p_k = l$，并且求出方案数。

考虑使用科技解决这个问题，我们先简化一下如果只需要求一个固定的 $l$。

$Q1:$ 看到很大的数据范围说明很难使用 $\tt Dp$ 来解决问题，况且欧拉数 $\tt Dp$ 是 $O(n^2)$ 的，已经难以推广。

考虑一个经典转化，单点方案数其实可以等价于概率乘上总方案，我们已知总方案我们不妨来计算概率。

$Q2:$ 很显然如果直接考虑所有排列的话我们不一定满足条件，我们不妨只考虑选取的数之后通过别的方式来计算出合法的排列个数。

> 显然这个合法的排列个数就是答案。这就意味着反演。

考虑使用概率密度函数：

> 我们考虑一个点在 $4 \times \frac{1}{4}$ 的长方形内区间 $[1, 2]$ 的分布概率，就是函数在 $[1, 2]$ 区间的积分。
>
> 这个 $\frac{1}{4}$ 其实就是个类似概率密度函数的东西。它用来描述连续型随机变量的输出值。我们想要知道它落在某个区间上的概率，只需要在这个区间上对概率密度函数作积分即可。

这样转化的好处在于我们可以很轻松地添加一个数。

$Q3:$ 我们选择数的方案数事实上是会被之前选择的某些数限制了，我们将其放到实数上这样可以解除这样的限制。

有 $n$ 个数，每个数的取值是实数 $[0, 1]$，满足有 $m$ 个下降。

建立模型来表示值 $x$ 落在某个区间的概率。

设特定位置为 $k$ 的总方案数是 $\alpha_k$ 我们可以得出转化关系。

这样考虑位置 $j$ 对概率密度函数的贡献就是 $\dfrac{\alpha_j }{(j - 1)!(n - j)!}\times x^{j - 1}(1 - x)^{n-  j}$。

> 考虑任何一个合法的排列，我们将其分成 $< x$ 和 $> x$ 两部分，因为没有数相同所以 $< x$ 占据的位置是 $(j - 1)!$ 排列中的一种，$> x$ 同理。

$$
\sum_{j = 0} ^ {n - 1} \alpha_j\frac{x^j(1 - x)^{n - 1 - j}}{j!(n - 1 - j)!} = \sum_{j = 0} ^ {n - 1} f_jx^j = f(x)
$$
后面的部分表示的仅仅是上述的生成函数，考虑如果已知生成函数如何求出 $\alpha$。

考虑进行换元，我们不需要关注左边的常数，提出来 $x^{n - 1}$ 可以得到 $(\frac{x}{1 - x})^j$ 换元为 $y^j$。

之后考虑有 $\frac{x}{1 - x} = y$ 那么有 $x = \frac{y}{1 + y}$ 带入右边得到 $(\frac{y}{1 + y})^j$ 之后将左边的 $(1 - x)^{n - 1}$ 放到右边可以得到 $(\frac{1}{1 + y})^{1 - n}$。

我们重新写一下这个式子：
$$
\sum_{j = 0}^{n - 1}\alpha_j \frac{y^j}{j!(n - 1 - j)!} = \sum_{j = 0} ^{n - 1} f_j y^j (1 + y)^{n - 1 - j}
$$
之后将 $y$ 看成 $x$ 即可。

右边的形式不是很好，不好卷积，考虑展开一下：
$$
\begin{aligned}
\sum_{j = 0} ^ {n - 1} f_j x^j(1 + x) ^{n - 1 - j} &= \sum_{j = 0} ^ j f_j x^j \sum_{k = 0} ^ {n - j - 1} \binom{n - j - 1}{k} x^k \\\\
&= \sum_{j = 0} ^ j f_j x^j \sum_{k = 0} ^ {n - j - 1} \frac{(n - j - 1)!}{k!(n - j - k - 1)!} x^k \\\\
&= \sum_{j = 0} ^ j f_j (n - j - 1)! x^j \sum_{k = 0} ^ {n - j - 1} \frac{1}{k!(n - j - k - 1)!} x^k \\\\
&= \sum_{j = 0} ^ j x^j \times \frac{1}{(n - j - 1)!}\sum_{a + b = j} f_a(n - a -1)! \times \frac{1}{b!}
\end{aligned}
$$
显然后面就是一个卷积。

发现最终得到的系数有如下的情况，假设之前得到的系数是 $b_j$。

我们有等式：
$$
\alpha_k \frac{1}{k!(n - k - 1)!} = b_k \frac{1}{(n - k - 1)!}
$$
显然 $\alpha_k = b_k \times k!$。

---

### 生成函数

> 我们正式开始解决这个问题。

考虑设生成函数 $F(u, v, t)$ 表示有 $u$ 个 $>$，$v$ 个 $<$ 其中 $x$ 的取值为 $t$ 的生成函数，我们考虑自身的映射。
$$
F(t) = 1 + u\int_{0}^ t F(x) dx + v\int_{t}^{1} F(x) dx
$$
积分不好做，我们求导一下，注意 $F(0) = F(1) = 0$。
$$
F' = (u - v) F
$$
解微分方程得到：
$$
F = Ce^{(u - v)t}
$$
带入 $t = 0, t = 1$ 设 $I = \int_{0}^1 F(x) dx$ 可以列出二元方程：
$$
\begin{cases}
    1+vI=C\\\\
    1+uI=C\mathrm{e}^{(u-v)t}
\end{cases}
$$
得到：
$$
\begin{cases}
    C &= \dfrac{u-v}{u-v\mathrm{e}^{u-v}}\\\\
    F &= \dfrac{(u-v)\mathrm{e}^{(u-v)t}}{u-v\mathrm{e}^{u-v}}
\end{cases}
$$
我们考虑左右两边的情况，不要忘记我们的 $F$ 是表示排列末尾的情况，所以我们合并的时候对于右边的生成函数需要倒置。

我们设 $x$ 表示元素的总个数，设 $y$ 表示下降的个数。

对于左边我们换元 $u = xy, v = x$ 对于右边是 $u = x, v = xy$。

之后写出函数：
$$
\begin{aligned}
    L &= \frac{(1-y)\mathrm{e}^{x(1-y)t}}{(1-y\mathrm{e}^{x(1-y)})}\\\\
    R &= \frac{(y-1)\mathrm{e}^{x(y-1)t}}{(y-\mathrm{e}^{x(y-1)})}\\\\
    &= \frac{(1-y)\mathrm{e}^{x(y-1)(t-1)}}{(1-y\mathrm{e}^{x(1-y)})}
\end{aligned}
$$

---

#### 推导

考虑取 $p, q$ 分别表示左右两边的形式幂级数，设 $n_1 = k - 1, n_2 = n - k$。

合并生成函数可以得到：
$$
[ y^m p^{n_1} q^{n_2} ] (1-y)^2 \frac{\mathrm{e}^{pt(1-y)+q(1-t)(1-y)}}{(1-y\mathrm{e}^{p(1-y)})(1-y\mathrm{e}^{q(1-y)})}
$$
发现对于 $p, q$ 都附带一个 $(1 - y)$ 考虑提出来：
$$
(1-y)^{n+1} \frac{\mathrm{e}^{pt+q(1-t)}}{(1-y\mathrm{e}^{p})(1-y\mathrm{e}^{q})}
$$
我们既然需要提取系数，就考虑因式分解能否得到一个简单的形式：
$$
(1-y)^{n+1} \mathrm{e}^{pt+q(1-t)} \frac 1{\mathrm{e}^p -\mathrm{e}^q} \left(\frac{\mathrm{e}^p}{1-y\mathrm{e}^p} - \frac{\mathrm{e}^q}{1-y\mathrm{e} ^q}\right)
$$
很遗憾我们出现了 $\dfrac{1}{e^p - e^q}$ 这样出现在指数上的生成函数我们不能求出系数，试试求导（套路）。
$$
\begin{aligned}
& \quad (1-y)^{n+1} \mathrm{e}^{pt+q(1-t)} \frac{p-q}{\mathrm{e}^p-\mathrm{e}^q}\left(\frac{\mathrm{e}^p}{1-y\mathrm{e}^p} - \frac{\mathrm{e}^q}{1-y\mathrm{e}^q}\right) \\
\end{aligned}
$$
考虑这个 $e^p - e^q$ 肯定是需要消除的，我们唯一能做的就是将其看成一体，也就是之后肯定需要考虑 $p - q$，我们决心将 $e^q$ 除掉。
$$
\begin{aligned}
& = (1-y)^{n+1} \mathrm{e}^{pt+q(1-t)} \mathrm{e}^{-q} \frac{ (p-q) }{\mathrm{e}^{p-q}-1}\left(\frac{\mathrm{e}^p}{1-y\mathrm{e}^p} - \frac{\mathrm{e}^q}{1-y\mathrm{e}^q}\right) \\\\
& = (1-y)^{n+1} \mathrm{e}^{(p-q)t} B(p-q) \left(\frac{\mathrm{e}^p}{1-y\mathrm{e}^p} - \frac{\mathrm{e}^q}{1-y\mathrm{e}^q}\right)
\end{aligned}
$$
后面的 $B(x)$ 表示伯努利数 $B(x) = \dfrac{x}{e^x - 1}$，这个很好处理。

然后考虑提取右边部分的系数，将 $(1 - y)^{n + 1}$ 放入。为了方便两部分可以看成 $G(x) = F(e^x) = \dfrac{x}{1 - yx} \times (1 - y)^{n + 1}$。

直接展开：
$$
F(x) = [y^m] \sum_{i \ge 0} (1 - y)^{n + 1} y^i x^{i + 1}
$$
我们知道我们迟早要带入 $F(e^x)$ 我们看看 $F$ 是否有什么性质。

我们继续展开 $F$：
$$
F(x) = \sum_{j = 0} ^ {n - 1} x^{j + 1} \binom{n + 1}{m - j} (-1)^{m - j}
$$
因为 $x^{j + 1}$ 不好看我们设 $xf(x) = F(x)$。

对于解决这类问题我们通常使用求导，但是这里其实可以偷个懒，因为没有别的项，两项的递推很好求出，直接作比即可。
$$
\begin{aligned}
    (n-m+1+i)f_i &= -(m-i+1)f_{i-1} + [i=0]C\\\\
    (n-m+1)f(x) + xf'(x)&= -mxf(x) + x^2 f'(x) + C\\\\
    (n-m+1+mx)f(x) + (x-x^2)f'(x)&=C
\end{aligned}
$$
这里的 $f_0$ 事实上就是 $\binom{n + 1}{m} (-1)^m$，为了方便我们直接让其为 $(n - m + 1)\binom{n + 1}{m}(-1)^m$。

之后带入 $G$ 得到：
$$
\begin{aligned}
    (n+1-m+m\mathrm e^x)g(x)+(1-\mathrm e^x)g'(x) &=C\\\\
    ((n-m)\mathrm e^{-x} +m+1)G + (\mathrm e^{-x}-1)G' &= C
\end{aligned}
$$
考虑展开求出 $G$，提取 $[x^i]$ 得到：
$$
\begin{aligned}
(n-m)\sum\limits_{j=1}^i \frac{(-1)^j}{j!} G_{i-j} &+ (n+1) G_i \\\\
+\sum\limits_{j=1}^i \frac{(-1)^j}{j!} (i-j+1) G_{i-j+1} &= [i=0]C
\end{aligned}
$$
最终可以得到：
$$
\begin{aligned}
(n-i+1)G_i &= [i=0]C \\\\
-(n-m)\sum\limits_{j=0}^{i-1} G_j \frac{ (-1)^{i-j} }{(i-j)!} &-\sum\limits_{j=1}^{i-1} j G_j \frac{ (-1)^{i-j+1} }{(i-j+1)!}
\end{aligned}
$$
直接使用半在线卷积即可。

最后来处理原式：
$$
(1-y)^{n+1} \mathrm{e}^{(p-q)t} B(p-q) \left(\frac{\mathrm{e}^p}{1-y\mathrm{e}^p} - \frac{\mathrm{e}^q}{1-y\mathrm{e}^q}\right)
$$
我们只举一边的例子，考虑求 $e^{(p - q)t}B(p - q)G(p)$，令 $q = rp$ 进行换元。
$$
\begin{aligned}
&\sum_{i \ge 0} [ p^{n - i - 1} ] G(p) \times [r^{n2}] (1 - r) ^ i \times [p^i] B(p)e^{pt} \\\\
= & \sum_{i \ge 0} Q_{n - i - 1} \times [p^i] B(p) e^{pt}, Q_{n - i - 1} = [p^{n - i - 1} ] G(p) \times [r^{n2}](1 - r) ^ i \\\\
= & [p^{n - 1}] Q(p) B(p) e^{pt} \\\\
= & \sum_{i \ge 0} \frac{t^i}{i!} [p ^ {n - 1 - i}] Q(p)B(p)
\end{aligned}
$$
这样就做完了，复杂度是 $O(\dfrac{n \log^2 n}{\log \log n})$。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;
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

const int maxn = 1.2e6 + 500;
const int mod = 998244353, G = 3, invG = 332748118;
int n, m, K;

int ksm(int x,int mi) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

int inv[maxn], w[2][21][maxn];
int Inv(int x) { return x < maxn ? inv[x] : ksm(x, mod - 2); }
int lim, len, rev[maxn];

void getrev(int x) {
    for(lim = 1, len = 0; lim <= x; lim <<= 1, ++ len) ;
    for(int i = 0; i < lim; ++ i) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (len - 1));
}

void NTT(int *A,int op = 1) {
    for(int i = 0; i < lim; ++ i) if(i < rev[i]) swap(A[i], A[rev[i]]);
    for(int mid = 1, ts = 1; mid < lim; mid <<= 1, ++ ts) {
        for(int j = 0, c = (mid << 1); j < lim; j += c) {
            const int* w1 = w[op == 1 ? 0 : 1][ts];
            for(int k = 0; k < mid; ++ k) {
                int x = A[j + k], y = 1ll * w1[k] * A[j + k + mid] % mod;
                A[j + k] = (x + y) % mod;
                A[j + k + mid] = (x - y + mod) % mod;
            }
        }
    }
    if(op != 1) {
        int z = Inv(lim);
        for(int i = 0; i < lim; ++ i) A[i] = 1ll * A[i] * z % mod;
    }
}

int g[maxn], fac[maxn], finv[maxn];

void init(int x) {
    fac[0] = 1;
    for(int i = 1; i <= x; ++ i) fac[i] = 1ll * fac[i - 1] * i % mod;
    finv[x] = ksm(fac[x], mod - 2);
    for(int i = x - 1; i >= 0; -- i) finv[i] = 1ll * finv[i + 1] * (i + 1) % mod;
    for(int i = 1; i < maxn; ++ i) inv[i] = ksm(i, mod - 2);
    for(int t = 1; t <= 20; ++ t) {
        int buf0 = ksm(G, (mod - 1) / (1 << t));
        int buf1 = ksm(invG, (mod - 1) / (1 << t));
        w[0][t][0] = w[1][t][0] = 1;
        for(int k = 1; k < (1 << t); ++ k) {
            w[0][t][k] = 1ll * w[0][t][k - 1] * buf0 % mod;
            w[1][t][k] = 1ll * w[1][t][k - 1] * buf1 % mod;
        }
    }
}
int C(int a,int b) {
    if(a < b) return 0;
    return 1ll * fac[a] * finv[b] % mod * finv[a - b] % mod;
}
int sign(int x) { return (x & 1) ? mod - 1 : 1; }
#define cpy(a, b, c) memcpy(a, b, sizeof(int) * (c))
#define clr(a, b) memset(a, 0, sizeof(int) * (b))
void Solve(int l,int r) {
    if(l == r) {
        g[l] = 1ll * g[l] * Inv(n - l + 1) % mod;
        return ;
    }
    int mid = (l + r) >> 1, ln = mid - l + r - l + 2;
    Solve(l, mid);
    getrev(ln);
    static int s1[maxn], s2[maxn], s3[maxn], s4[maxn], ans[maxn];
    clr(s1, lim), clr(s2, lim), clr(s3, lim), clr(s4, lim), clr(ans, lim);
    for(int i = 0; i <= mid - l; ++ i) {
        s1[i] = 1ll * g[i + l] * (n - m) % mod;
        s2[i] = 1ll * g[i + l] * (i + l) % mod;
    }
    for(int i = 0; i <= r - l; ++ i) {
        s3[i] = 1ll * sign(i) * finv[i] % mod;
        s4[i] = 1ll * sign(i + 1) * finv[i + 1] % mod;
    }
    if(r - l <= 100 || mid - l <= 50) {
        for(int sr = mid + 1 - l; sr <= r - l; ++ sr) for(int sl = 0; sl <= mid - l && sl <= sr; ++ sl) {
            ans[sr] = (ans[sr] + 1ll * s1[sl] * s3[sr - sl] % mod + 1ll * s2[sl] * s4[sr - sl] % mod) % mod;
        }
    }
    else {
        NTT(s1), NTT(s2), NTT(s3), NTT(s4);
        for(int i = 0; i < lim; ++ i) ans[i] = (1ll * s1[i] * s3[i] % mod + 1ll * s2[i] * s4[i] % mod) % mod;
        NTT(ans, -1);
    }
    for(int i = mid + 1; i <= r; ++ i) g[i] = (g[i] - ans[i - l] + mod) % mod;
    Solve(mid + 1, r);
//    if(r - l > 5000) printf("(%d, %d)\n", l, r);
}

void Inv(int *A, int* B, int ln) {
    if(ln == 1) return B[0] = Inv(A[0]), void();
    static int s1[maxn];
    Inv(A, B, (ln + 1) >> 1);
//    printf("ln = %d\n", ln);
//    for(int i = 0; i < ln; ++ i) printf("%d ", B[i]);
//    puts("\n ");
    getrev(ln << 1);
    cpy(s1, A, ln), clr(s1 + ln, lim);
    NTT(s1), NTT(B);
    for(int i = 0; i < lim; ++ i) B[i] = 1ll * B[i] * (2 - 1ll * s1[i] * B[i] % mod + mod) % mod;
    NTT(B, -1);
    for(int i = ln; i < lim; ++ i) B[i] = 0;
}

int P[maxn], Q[maxn], B[maxn], rs[maxn], F[maxn], T[maxn], sA[maxn], ans[maxn];
/*
5 1 1
1 6 3 4 9
*/

void Mul(int* A,int* B,int* as,int ln) {
    getrev(ln << 1);
    static int a[maxn], b[maxn];
    clr(a, lim), clr(b, lim);
    cpy(a, A, ln), cpy(b, B, ln);
    NTT(a), NTT(b);
    for(int i = 0; i < lim; ++ i) as[i] = 1ll * a[i] * b[i] % mod;
    NTT(as, -1);
}

signed main() {
	int i, j;
    r1(n, m, K);
    int n1 = K - 1, n2 = n - K;
    init(n + 5);
    g[0] = 1ll * sign(m) * C(n + 1, m) % mod * (n - m + 1) % mod;
    Solve(0, n - 1);
//    for(i = 0; i < n; ++ i) printf("%d ", g[i]);
    getrev(n);
    for(i = 0; i <= n; ++ i) B[i] = finv[i + 1];
    Inv(B, rs, n + 1), cpy(B, rs, n + 1);

//    for(i = 0; i < n; ++ i) r1(B[i]);
//    Inv(B, rs, n);
//    for(i = 0; i <= n; ++ i) printf("%d ", B[i]);

    for(i = 0; i < n; ++ i) Q[n - i - 1] = 1ll * g[n - i - 1] * sign(n2) % mod * C(i, n2) % mod;
//    for(i = 0; i < n; ++ i) printf("%d ", Q[i]);
//    puts("");
    Mul(Q, B, Q, n);
    for(i = 0; i < n; ++ i) P[n - i - 1] = 1ll * g[n - i - 1] * sign(n1 - i) % mod * C(i, n1) % mod;
    Mul(P, B, P, n);
    for(i = 0; i < n; ++ i) F[i] = 1ll * (Q[n - i - 1] - P[n - i - 1] + mod) * finv[i] % mod;
    for(i = n; i >= 1; -- i) F[i] = 1ll * F[i - 1] * Inv(i) % mod;
    F[0] = 0;

//    for(i = 0; i < n; ++ i) printf("%d ", F[i]);
//    puts("");
//    puts("OK");
    for(i = 0; i < n; ++ i) F[i] = 1ll * F[i] * fac[n - i - 1] % mod;
    for(i = 0; i < n; ++ i) T[i] = finv[i];
    Mul(F, T, sA, n);
    for(i = 0; i < n; ++ i) ans[i] = 1ll * sA[i] * fac[i] % mod;

    int left = 0;
    for(i = 0; i <= m; ++ i) left = (left + 1ll * C(n + 1, i) * ksm(m + 1 - i, n) % mod * sign(i) % mod) % mod;
//    printf("left = %d\n", left);
    for(i = 0; i < n; ++ i) left = (left - ans[i] + mod) % mod;
    left = 1ll * left * Inv(n) % mod;
    for(i = 0; i < n; ++ i) ans[i] = (ans[i] + left) % mod;
//    printf("left = %d\n", left);
    for(i = 0; i < n; ++ i) printf("%d ", ans[i]);
    puts("");
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

---

### 后记

我也不知道我为啥跑得这么慢，反正最劣解就是了，但是其实半在线卷积没有那么难写。

从大概是去年这个时候接触多项式，就听到了这个是一个神仙题，看着 $\tt alpha1022$ 的博客感慨 `这是什么鬼啊！！！`。

> 之后认识他是我在 $\tt luogu$ 群问问题的时候，这位大佬来教我的，~~结果发现我是式子看错了~~。

算继省选和诸多支线赛事失利的一种安慰吧，来写写这个题。

感谢给予我实际帮助的所有人：

- $\tt expwmh$ 帮我推了 $1$ 个式子，~~虽然最终也没有用到~~。

- $\tt \text{野兽先辈}$，数竞国家集训队大佬，解答了我 $4$ 个困惑。
- $\tt silhouette$ 给予精神上的支持。
- $\tt macesuted$ $D$ 了我好久。
- $\tt hater$ 鸽鸽好强！！！
