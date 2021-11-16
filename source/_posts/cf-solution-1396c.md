---
title: CF1396C Monster Invaders
date: 2021-11-16 20:14:00
tags:
  - Dp
  - 贪心
categories:
  - CF题解
---

<h3><center>CF1396C Monster Invaders</center></h3>

> [CF1396C Monster Invaders](https://www.luogu.com.cn/problem/CF1396C)

建议不要看中文，直接看英文题面，里面有很重要的一句话：**一开始都没有装弹，一次只能装一把枪**。

这个东西决定了我们贪心的方案，不然的话可能像我以前或者一开始一样当成同时开始冷却 /qd

----

这边再吐槽一下，之前已知不会订正，现在想想还是挺简单的。

---

因为 $r_1 \le r_2 \le r_3$。

首先考虑总共只有 $3$ 种打的方案：

- $a_i \times r_1 + r_3$。
- $a_i \times r_1 + r_1 + r_1$。
- $r_2 + r_1$。

而且考虑走的方法，我们会考虑一开始直接走到头，之后一次回来，发现距离是 $2d$。

我们考虑相邻两个一起搞定，那么乍一看是 $3d$ 但是每两个相邻之间本质上只有 $d$，均摊一下还是 $2d$。

所以我们只需要考虑相邻的部分，直接考虑当前是否打死，设 $f(i, 0/1)$ 表示当前考虑到位置 $i$，$\tt boss$ 还有血量 $0/1$ 的最小代价。

直接分类讨论暴力转移即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

namespace Legendgod {

namespace Read {
//    #define Fread
    #ifdef Fread
    const int Siz = (1 << 21) + 5;
    char buf[Siz], *iS, *iT;
    #define gc() (iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iT == iS ? EOF : *iS ++ ) : *iS ++)
    #define getchar gc
    #endif // Fread

    template <typename T>
    void r1(T &x) {
        x = 0; int f(1); char c(getchar());
        for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
        for(; isdigit(c); c = getchar()) x = (x * 10) + (c ^ 48);
        x *= f;
    }
    template <typename T, typename...Args>
    void r1(T &x, Args&...arg) {
        r1(x), r1(arg...);
    }

}

using namespace Read;

const int maxn = 1e6 + 5;
typedef long long int64;
int64 f[maxn][2];
int64 r[4], d, a[maxn];
int n;

signed main() {
    int i, j;
    r1(n, r[1], r[2], r[3], d);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    memset(f, 0x3f, sizeof(f));
    f[1][0] = r[1] * a[1] + r[3];
    f[1][1] = min(r[2], r[1] * a[1] + r[1]);
    function<void(int64 &x, const int64 &c)> Upd;
    Upd = [&](int64 &x, const int64 &c) { x < c ? 0 : x = c; };
    for(i = 1; i < n; ++ i) {
        // 0 -> 0
        Upd(f[i + 1][0], f[i][0] + d + a[i + 1] * r[1] + r[3]);
        // 0 -> 1
        Upd(f[i + 1][1], f[i][0] + d + min(a[i + 1] * r[1] + r[1], r[2]));
        // 1 -> 1
        Upd(f[i + 1][1], f[i][1] + d + a[i + 1] * r[1] + r[1] + 2 * d + r[1]);
        Upd(f[i + 1][1], f[i][1] + d + r[2] + 2 * d + r[1]);
        // 1 -> 0
        Upd(f[i + 1][0], f[i][1] + d + a[i + 1] * r[1] + r[3] + 2 * d + r[1]);
        Upd(f[i + 1][0], f[i][1] + d + a[i + 1] * r[1] + r[1] + 2 * d + r[1] + r[1]);
        Upd(f[i + 1][0], f[i][1] + d + r[2] + 2 * d + r[1] + r[1]);
        if(i == n - 1) {
            Upd(f[i + 1][0], f[i][1] + d + a[i + 1] * r[1] + r[3] + d + r[1]);
        }
    }
    printf("%lld\n", f[n][0]);
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }

```

