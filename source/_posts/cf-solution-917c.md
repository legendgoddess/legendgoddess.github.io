---
title: CF917C Pollywog
date: 2021-09-24 10:14:49
tags: 
  - Dp
  - 矩阵
categories: 
  - CF题解
---


<h3><center>CF917C Pollywog</center></h3>

> [CF917C Pollywog](https://www.luogu.com.cn/problem/CF917C)

发现 $x$ 是比较小的我们考虑直接状压。

之后发现 $n$ 是比较大的考虑使用矩阵加速进行运算。

矩阵加速其实不一定只能使用加减，使用 $\min, \max$ 也是可以的，具体来说需要满足一些性质。

我们不妨设这个两个运算符运算符是 $\oplus, \otimes$。

显然对于我们矩阵乘法的定义，最终得到的矩阵 $C(i, j)$ 肯定是通过 $(A_{i, 1} \otimes B_{1, j}) \oplus (A_{i, 2} \otimes B_{2, j}) \oplus \dots$ 得到的。

我们进行矩阵加速需要满足结合律，也就是 $(AB)C = A(BC)$ 我们直接拆开可以得到几个性质：

- $\otimes$ 满足交换律，结合律
- $\otimes$ 对于 $\oplus$ 满足分配率，也就是说 $A \otimes(B \oplus C) = (A\otimes B) \oplus (A\otimes C )$

------

显然对于 $\min, +$ 两个操作是满足这个的。

> 别忘记构造单位矩阵。

我们考虑直接进行矩阵加速即可。

但是发现直接状压所有的状态是不可能的，有 $2^k$ 种。我们考虑只状压 $x$ 在内部的情况，因为外部同样可以推出。对于 $x$ 在内部可以考虑对于 $x = \dfrac{k}{2}$ 的情况，状态数最多只有 $\binom{8}{4} = 70$ 可以进行加速。

每次按照题目的意思对于最左边的进行转移，肯定是向右移动一个位置，暴力找即可。

如果对于状压的 $k$ 最左边没有值，就直接转移不需要花费。

对于中间有若干个特殊石头的情况只是单纯来恶心的，就是分成 $q$ 段分次转移，之后对于这些石头只需要将最左边位置有人的进行加贡献即可。

代码中状压最右边的一位实际上就是最左边的位置。

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
typedef long long ll;
int X, K, n, Q;
int tot;

const ll inf = 0x3f3f3f3f3f3f3f3f;

struct Matrix {
    ll a[71][71];
    Matrix(void) { memset(a, 0x3f, sizeof(a)); for(int i = 1; i <= 70; ++ i) a[i][i] = 0; }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 1; i <= tot; ++ i) res.a[i][i] = inf;
        for(int i = 1; i <= tot; ++ i) for(int j = 1; j <= tot; ++ j) {
            for(int k = 1; k <= tot; ++ k) {
                res.a[i][j] = min(res.a[i][j], a[i][k] + z.a[k][j]);
            }
        }
        return res;
    }
}tmp, F;

int id[maxn];

bool calc(int x) {
    int sum(0);
    for(int i = 1; i <= 8; ++ i) if((x >> (i - 1)) & 1) ++ sum;
    return (sum == X);
}

struct Node {
    int p; ll w;
    int operator < (const Node &z) const {
        return p < z.p;
    }
}st[maxn];

ll c[maxn];
/*
 ... 1
the stone which more closed the right place is the first one
*/

void ksm(Matrix &a, Matrix x,int mi) {
    while(mi) {
        if(mi & 1) a = a * x;
        mi >>= 1;
        x = x * x;
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(X, K, n, Q);
    for(i = 1; i <= K; ++ i) r1(c[i]);
    for(i = 1; i <= Q; ++ i) r1(st[i].p, st[i].w);
    sort(st + 1, st + Q + 1);
    for(tot = i = 0; i < (1 << K); ++ i) if(calc(i)) id[i] = ++ tot;
//    printf("tot = %d\n", tot);
    for(i = 1; i <= tot; ++ i) tmp.a[i][i] = inf;
    for(i = 1; i < (1 << K); ++ i) if(id[i]) {
        if(i & 1) {
            for(j = 1; j <= K; ++ j) if(!(i & (1 << j)))
                tmp.a[id[i]][id[((1 << j) | i) >> 1]] = c[j];
        }
        else tmp.a[id[i]][id[i >> 1]] = 0;
    }
    ll sum(0);
    int last(1);
    for(i = 1; i <= Q; ++ i) {
        if(st[i].p > n - X) { sum += st[i].w; continue; }
        ksm(F, tmp, st[i].p - last);
        last = st[i].p;
        for(j = 1; j < (1 << K); j += 2) if(id[j]) {
            for(int k = 1; k <= tot; ++ k)
                F.a[k][id[j]] += st[i].w;
        }
    }
    ksm(F, tmp, n - X + 1 - last);
    printf("%lld\n", F.a[1][1] + sum);
	return 0;
}
```



