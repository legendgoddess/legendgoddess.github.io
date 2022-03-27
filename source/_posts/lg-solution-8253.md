---
title: "[NOI Online 2022 提高组] 如何正确地排序 题解"
date: 2022-3-27 14:10:00
tags:
  - 树状数组
categories:
  - 洛谷题解
---

考虑求 $\tt K = 4$ 的情况，本质就是考虑每个数的贡献，发现其贡献次数就是：

$$
\begin{aligned}
a_{1, i} - a_{1, j} &\ge a_{2, j} - a_{2, i} \\\\
a_{1, i} - a_{1, j} &\ge a_{3, j} - a_{3, i} \\\\
a_{1, i} - a_{1, j} &\ge a_{4, j} - a_{4, i} \\\\
\end{aligned}
$$

同时满足这些条件的方案数，这个东西可以直接使用三维偏序做。

但是我们可以不这样，考虑我们求 $f(i, j)$ 本质上就是四个位置的最大值最小值相加。

我们考虑对于最小值进行 $\tt min-max$ 容斥：

$$
\begin{aligned}
&\max(A, B, C, D) + \min(A, B, C, D) =\\\\
 &A + B + C + D \\\\
&- \max(A, B) - \max(A, C) - \max(A, D) - \max(B, C) - \max(B, D) - \max(C, D)\\\\
 &+ \max(A, B, C) + \max(A, C, D) + \max(A, B, D) + \max(B, C, D)
\end{aligned}
$$

考虑 $m = 2$ 的情况，考虑枚举 $i$，找有多少个 $j$ 满足 $a_{1, i} - a_{2, i} \ge a_{2, j} - a_{1, j}$。考虑直接作差之后维护一下桶即可，钦定 $i < j$。

考虑 $m = 3$ 的情况，考虑使用二维偏序，对于每个位置 $i$ 维护三个东西对于每一维最大值都做一遍即可。

发现我没有写过这些结构的维护，所以写了很久，代码还是比较简洁的，主要问题就是要保证偏序我们需要将查询也加入排序。

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

const int maxn = 2e5 + 5;
typedef long long int64;
int n, m, a[5][maxn];
const int N = 2e5 + 3;
struct Seg {
    int64 t[maxn << 2];
    int lowbit(int x) { return x & -x; }
    void add(int p,int c) { for(; p <= 2 * N; p += lowbit(p)) t[p] += c; }
    int64 ask(int p) { int64 res(0); for(; p > 0; p -= lowbit(p)) res += t[p]; return res; }
    void clear() { for(int i = 0; i <= 2 * N; ++ i) t[i] = 0; }
}T;

int64 S2(int x,int y) {
    if(x > m || y > m) return 0;
    int64 res(0);
    T.clear();
    for(int i = 1; i <= m; ++ i) {
        int tmp = a[x][i] - a[y][i];
        T.add(N - tmp, 1);
    }
    for(int i = 1; i <= m; ++ i) {
        int tmp = a[x][i] - a[y][i];
        res += T.ask(N + tmp) * a[x][i];
    }
    T.clear();
    for(int i = 1; i <= m; ++ i) {
        int tmp = a[y][i] - a[x][i];
        T.add(N - tmp, 1);
    }
    for(int i = 1; i <= m; ++ i) {
        int tmp = a[y][i] - a[x][i];
        res += T.ask(N + tmp - 1) * a[y][i];
    }
    auto Force = [&]() -> int64{
        int64 rs(0);
        for(int i = 1; i <= m; ++ i) for(int j = 1; j <= m; ++ j) {
            rs += max(a[x][i] + a[x][j], a[y][i] + a[y][j]);
        }
        return rs;
    };
    return res * 2;
}

struct Node {
    int x[2], val, id;
    int operator < (const Node& z) {
        return x[0] < z.x[0] || (x[0] == z.x[0] && id < z.id);
    }
}v[maxn << 1];

int64 S3(int x,int y,int z) {
    if(x > n || y > n || z > n) return 0;
    vector<int> vc; vc.emplace_back(0), vc.emplace_back(x), vc.emplace_back(y), vc.emplace_back(z);
    int64 res(0);
    for(int k = 1; k <= 3; ++ k) {
        for(int i = 1; i <= m; ++ i)
        {
            v[i].id = i; v[i].val = a[vc[k]][i];
			v[i + m].id = 0, v[i + m].val = a[vc[k]][i];
            for(int s = 1, ed = 0; s <= 3; ++ s) if(s != k)
            {
                v[i].x[ed ++] = a[vc[k]][i] - a[vc[s]][i];
				v[i + m].x[ed - 1] = a[vc[s]][i] - a[vc[k]][i] + (s < k);
            }
        }
        sort(v + 1, v + 2 * m + 1), T.clear();
		for(int i = 1; i <= 2* m; ++ i) {
			if(v[i].id != 0) res += v[i].val * T.ask(N + v[i].x[1]);
			else T.add(N + v[i].x[1], 1);
		}
    }
    // auto Force = [&]() -> int64{
        // int64 rs(0);
        // for(int i = 1; i <= m; ++ i) for(int j = 1; j <= m; ++ j) {
            // rs += max(max(a[x][i] + a[x][j], a[y][i] + a[y][j]), a[z][i] + a[z][j]);
        // }
        // return rs;
    // };
    return res * 2;
}

signed main() {
	int i, j;
    int64 ans(0);
    r1(n, m);
    for(i = 1; i <= n; ++ i) for(j = 1; j <= m; ++ j) r1(a[i][j]), ans += a[i][j] * 2ll * m;
    if(n == 4) {
        ans -= S2(1, 2) + S2(1, 3) + S2(1, 4) + S2(2, 3) + S2(2, 4) + S2(3, 4);
        ans += S3(1, 2, 3) + S3(1, 2, 4) + S3(1, 3, 4) + S3(2, 3, 4);
    }
    if(n == 3) {
        ans = S3(1, 2, 3);
        for(i = 1; i <= n; ++ i) for(j = 1; j <= m; ++ j) a[i][j] = N - 1 - a[i][j];
        ans += - S3(1, 2, 3) + 2ll * m * m * (N-1);
    }
    printf("%lld\n", ans);
	return 0;
}
/*
2 5
9 10 4 10 3
7 7 8 10 2
*/
}


signed main() { return Legendgod::main(), 0; }//

```
