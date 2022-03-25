---
title: "P6619 [省选联考 2020 A/B 卷] 冰火战士 题解"
date: 2022-3-25 08:19:00
tags:
  - 树状数组
categories:
  - 省选题解
---

[[省选联考 2020 A/B 卷] 冰火战士 - 洛谷](https://www.luogu.com.cn/problem/P6619)

奇怪的题面可以看成求 $\tt 2\max(\min(vl, vr))$，其中 $vl, vr$ 表示左右两边的能量和。

这个东西显然可以考虑二分，之后考虑假设我们二分出来一个答案我们需要通过二分找到每边符合条件的战士之和。

但是其实不需要这样，很明显发现我们是尽量让战士越多越好，当然这个条件肯定是有一个临界值，可以发现我们只需要考虑**冰**战士比**火**战士能量多和少的情况。而且对于这个情况我们还能发现温度肯定是某个战士的温度进行 $\pm 1$ 的偏移。

考虑直接对于这个温度进行二分，使用树状数组进行二分，这个东西本质就和倍增是一样的。

之后就是考虑无解是怎么判的，考虑无解就是取任意温度双方都有一方是不能参赛的，也就是冰战士的最低温度比火战士的最高温度要高，不然一定存在解。

**细节：** 冰系战士是不低于，火系战士是不高于。我们考虑什么时候一个战士是不能参赛的，对于冰系来说就是温度严格低于，火系是严格高于。如果考虑让冰系贡献减去火系贡献的时候，加入冰系就是在其温度的时候，减去火系就是在其温度 $+ 1$ 的时刻。

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
typedef long long int64;
int n, m, tn;

int tot(0); int64 tp[maxn];
struct Seg {
    int64 t[maxn << 2];
    int lowbit(int x) { return x & -x; }
    void Add(int p,int64 c) { for(; p <= tot; p += lowbit(p)) t[p] += c; }
    int64 Ask(int p) { int64 res(0); for(; p > 0; p -= lowbit(p)) res += t[p]; return res; }
    int64& operator [] (const int& x) { return t[x]; }
    const int64& operator [] (const int& x) const { return t[x]; }
}T[2];

struct Quer {
    int opt, op, x; int64 y;
}q[maxn];

signed main() {
	int i, j;
    r1(n), tn = n;
    for(i = 1; i <= n; ++ i) {
        r1(q[i].opt);
        if(q[i].opt == 1) r1(q[i].op, q[i].x, q[i].y), tp[++ tot] = q[i].x;
        else r1(q[i].op);
    }
    sort(tp + 1, tp + tot + 1);
    tot = unique(tp + 1, tp + tot + 1) - tp - 1;
    int64 sumfir(0);
    for(int pos = 1; pos <= n; ++ pos) {
        if(q[pos].opt == 1) {
            q[pos].x = lower_bound(tp + 1, tp + tot + 1, q[pos].x) - tp;
            if(q[pos].op == 0) T[0].Add(q[pos].x, q[pos].y);
            else T[1].Add(q[pos].x + 1, q[pos].y), sumfir += q[pos].y;
        }
        else {
            auto nq = q[q[pos].op];
            if(nq.op == 0) T[0].Add(nq.x, - nq.y);
            else T[1].Add(nq.x + 1, - nq.y), sumfir -= nq.y;
        }
//        printf("%d : %lld\n", pos, sumfir);
        int64 tmpi(0), tmpf(sumfir), ps(0), ansi(0);
        for(i = 20; i >= 0; -- i) {
            int x = (1 << i);
            if((ps | x) > tot) continue;
            int np = (ps | x);
            tmpi += T[0][np], tmpf -= T[1][np];
//            printf("%d : %d %d %d\n", pos, i, T[0][p], ans[p][1]);
            if(tmpi >= tmpf) { tmpi -= T[0][np], tmpf += T[1][np]; continue; }
            ps = np, ansi = tmpi;
        }
        int64 ansf = sumfir - T[1].Ask(ps + 1);
//        printf("%d : %lld %lld\n", pos, ansi, ansf);
        if((!ansf && !ansi) || (ansf > T[0].Ask(ps + 1))) { puts("Peace"); continue; }
        if((ansi && ps == tot) || ansf < ansi) {
            printf("%lld %lld\n", tp[ps], ansi << 1);
            continue;
        }
        ps = 0, tmpi = 0, tmpf = sumfir;
        for(i = 20; i >= 0; -- i) {
            int x = (1 << i);
            if((ps | x) > tot) continue;
            int np = (ps | x);
            tmpi += T[0][np], tmpf -= T[1][np];
            if(tmpi < tmpf || tmpf == ansf) { ps = np; continue; }
            tmpi -= T[0][np], tmpf += T[1][np];
        }
        printf("%lld %lld\n", tp[ps], ansf << 1);
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
