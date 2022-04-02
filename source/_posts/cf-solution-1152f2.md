---
title: "CF1152F2 Neko Rules the Catniverse (Large Version) 题解"
date: 2022-4-2 15:23:00
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1152F2 - Codeforces](https://codeforces.com/problemset/problem/1152/F2)

发现 $k, m$ 很小这个东西肯定有问题。

要么是直接通向要么可以使用矩阵快速幂。

> 感觉任意两个元素都是不同比较难处理，所以我们考虑进行 $\tt Dp$。

设 $f(i ,j, S)$ 表示当前值是 $i$，是序列长度为 $j$。

因为直接按照序列进行放置值域是不好确定的，我们考虑进行插入操作。

对于值域为 $i$ 我们需要权值 $\ge i - m$ 的位置在其前面，所以 $S$ 表示权值 $\ge i - m$ 的权值有哪些在序列中。

当然我们可以放在序列的开头，因为我们考虑值域是从小到大的，所以可以直接进行操作。

$$
\begin{aligned}
f(i, j, S) \times (popcount(S) + 1) &\to
f(i + 1, j + 1, (2S + 1)) \\\\
f(i, j, S) &\to f(i + 1, j + 1, (2S)) 
\end{aligned}
$$

直接矩阵快速幂即可。

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

const int maxn = 200 + 5;
constexpr int mod = 1e9 + 7;
int n, m, Up, K;

struct Matrix {
    int a[maxn][maxn];
    Matrix(void) { memset(a, 0, sizeof(a)); }
    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i <= Up; ++ i) {
            for(int j = 0; j <= Up; ++ j) {
                long long tmp(0);
                for(int k = 0; k <= Up; ++ k) {
                    tmp += 1ll * a[i][k] * z.a[k][j] % mod;
                }
                res.a[i][j] = (res.a[i][j] + tmp) % mod;
            }
        }
        return res;
    }
}trans, F;

signed main() {
	int i, j;
    r1(n, K, m); Up = (K << m);
    for(i = 0; i < K; ++ i)
    for(j = 0; j < (1 << m); ++ j) {
        int tp = ( (j << 1) & ( (1 << m) - 1) );
        int c = 1 + __builtin_popcount(j);
        trans.a[(i << m) + j][(i << m) + tp] = 1;
        if(i == K - 1) trans.a[(i << m) + j][Up] = c;
        else trans.a[(i << m) + j][((i + 1) << m) + tp + 1] = c;
    }
//    for(int i = 0; i <= Up; ++ i) for(int j = 0; j <= Up; ++ j) printf("%d%c", trans.a[i][j], " \n"[j == Up]);
    trans.a[Up][Up] = F.a[0][0] = 1;
    while(n) {
        if(n & 1) F = F * trans;
        trans = trans * trans;
        n >>= 1;
    }
    printf("%d\n", F.a[0][Up]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```




