---
title: "Bitset 和 __builtin 函数"
date: 2022-3-29 07:34:00
categories:
  - 算法
---

$\tt bitset$ 是一个比较神奇的东西，我感觉经常用来卡常数，毕竟这个东西有 $\frac{1}{w}$ 的常数。

> 事实证明，如果写埃氏筛用 $\tt bitset$ 比欧拉筛要快。

### 定义：

就是一个存了 $0/1$ 串的东西，每个位占用一个 $B$。

可以使用 `unsigned long long` 或者 `string` 进行初始化，默认值为全 $0$。

> $\color{red}\text{pay attention ! }$`string` 最右边的那一位是被放在 `bitset` 的位置 $0$ 的，数字同理。

```cpp
bitset<maxn> vis;//000...000
bitset<maxn> vis1(5);// 000...00101
bitset<maxn> vis2("100101"); // 000...100101
```

### $\tt Bitset$ 函数

- `flip()` 全部取反。 

- `filp(x)` 取反下标为 $x$ 的位置。

- `size()` 返回位数（大小）。

- `count()` 返回 $1$ 的个数。

- `any()` 返回是否有 $1$，有 $1$ 则为 $\tt true$。

- `none()` 返回是否没有 $1$，如果没有 $1$ 为 $\tt true$。

- `set()` 全部设置为 $1$。

- `set(x)` 将下标为 $x$ 的位置设置为 $1$。

- `reset()` 全部设置为 $0$。

- `reset(x)` 将下标为 $x$ 的位置设置为 $0$。

- `to_ulong()` 返回它转换为 $\tt unsigned\ long$ 的结果，如果超出范围则报错。

- `foo.to_ullong()` 返回它转换为 $\tt unsigned\ long\ long$ 的结果，如果超出范围则报错

- `foo.to_string()` 返回它转换为 $\tt string$ 的结果。

- `_Find_first()` 找到第一个为 $1$ 的下标，如果不存在就返回 $\tt bitset$ 的大小。

- `_Find_next(x)` 找到在 $x$ 之后的第一个为 $1$ 的下标（不包含 $x$），如果不存在就返回 $\tt bitset$ 的大小。

### $\tt \_\_builtin$

下面说的都有 $\tt unsigned\ int$ 和 $\tt unsigned\ long\ long$ 的版本，只要在函数名称末尾加 $\tt l$ 或者 $\tt ll$ 即可。

- `__builtin_ffs(x)` 返回 $x$ 的**最低位的** $1$ 是从后向前第几位。 

```cpp
int n = 10, m = 5; // 1010(2), 101(2)
printf("%d %d\n", __builtin_ffs(n), __builtin_ffs(m)); // 2, 1
```

- `__builtin_clz(x)` 返回 $x$ 左边前导零的个数。

```cpp
int n = 5;//101
printf("%d\n", __builtin_clz(n));//29 = 31 - 3 + 1
/*
具体来说就是总共有 32 个位置，左边有 29 个位置是 0.
为什么只有 31 呢？符号位！！！
所以 __builtin_clz(-1) = 0
*/
```

- `__builtin_ctz(x)` 返回 $x$ 右边 $0$ 的个数。

```cpp
int n = 6;// 110(2)
printf("%d\n", __builtin_ctz(n));// 1
```

- `__builtin_popcount(x)` 返回 $x$ 二进制位上有几个 $1$。

```cpp
int n = 7;
printf("%d\n", __builtin_popcount(n )); // 3
```

- `__builtin_parity(x)` 返回 $x$ 二进制上 $1$ 的奇偶性。

```cpp
int n = 7, m = 6;
printf("%d %d\n", __builtin_parity(n), __builtin_parity(m)); // 1 0
```

### 例题

[[Ynoi2017] 由乃的玉米田 - 洛谷](https://www.luogu.com.cn/problem/P5355)

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread

#ifdef Fread
#define getchar() ((p1 == p2 && p2 = (p1 = buf) + fread(buf, 1, 1 << 21, stdin)) ? EOF : *p1 ++)
char buf[1 << 21], *p1, *p2;
#endif // Fread

template <typename T>
void r1(T &x) {
    x = 0;
    char c(getchar());
    int f(1);
    for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
    for(; isdigit(c);c = getchar()) x = (x << 1) + (x << 3) + (c ^ 48);
    x *= f;
}

const int maxn = 1e5 + 5;
const int maxm = maxn << 1;

typedef int room[maxn];

int n, m;
int a[maxn], bel[maxn];

const int lim = 316;
const int Z = 1e5;

bitset<maxn * 2> B0, S0;

struct Node {
    int opt, l, r, val, id;
    int operator < (const Node &z) const {
        return (bel[l] == bel[z.l]) ? r < z.r : bel[l] < bel[z.l];
    }
}q[maxn];
int tot;
vector<int> ql[maxn], qr[maxn], qid[maxn];
int pre[maxn], maxl[maxn];
int cnt[maxn];

void Add(int x) {
    int w = a[x];
    ++ cnt[w];
    B0[Z + w + 1] = 1;
    S0[Z - w] = 1;
}
void Del(int x) {
    int w = a[x];
    -- cnt[w];
    if(cnt[w] == 0) B0[Z + w + 1] = 0, S0[Z - w] = 0;
}

int ans[maxn];

#define yes(_) ans[_] = 1

signed main() {
//    freopen(".in", "r", stdin);
//    freopen(".out", "w", stdout);
    int i, j, B;
    r1(n), r1(m);
    B = sqrt(n);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= n; ++ i) bel[i] = (i - 1) / B + 1;
    for(i = 1; i <= m; ++ i) {
        int opt, l, r, x;
        r1(opt), r1(l), r1(r), r1(x);
        if(opt != 4 || (opt == 4 && x > lim)) {
            q[++ tot] = (Node) {opt, l, r, x, i};
        }
        else {
            ql[x].push_back(l), qr[x].push_back(r), qid[x].push_back(i);
        }
    }
    int m1 = m;
    m = tot;
    sort(q + 1, q + m + 1);
    int L(1), R(0);
    for(i = 1; i <= m; ++ i) {
        int x = q[i].val, opt = q[i].opt;
        while(L > q[i].l) Add(-- L);
        while(R < q[i].r) Add(++ R);
        while(L < q[i].l) Del(L ++);
        while(R > q[i].r) Del(R --);
        if(opt == 1) { if((B0 & (B0 >> x)) != 0) ans[q[i].id] = 1; }
        else if(opt == 2) { if(((B0 >> (x + 1)) & S0) != 0) ans[q[i].id] = 1; }
        else if(opt == 3) {
            for(int j = 1; j * j <= x; ++ j) if(x % j == 0) {
                if(j <= Z && (x / j) <= Z && B0[j + Z + 1] == 1 && B0[x / j + Z + 1] == 1) {ans[q[i].id] = 1;break;}
            }
        }
        else {
            for(int j = 1; j * x <= Z; ++ j) if(B0[j + Z + 1] && B0[j * x + Z + 1]) { ans[q[i].id] = 1; break; }
        }
    }
//    Solve();
    for(int x = 1; x <= lim; ++ x) if(!ql[x].empty()) {
        int l(0);
        for(i = 1; i <= n; ++ i) {
            // pre[i] 表示 i 最晚出现的位置
            // maxl[r] 表示在二元组 (x, r) 中左端点出现的距离 r 最近的位置
            // 上面是在左边算
            int w = a[i];
            pre[w] = i;
            if(w * x <= Z) l = max(l, pre[w * x]);
            if(w % x == 0) l = max(l, pre[w / x]);
            maxl[i] = l;
        }
        for(unsigned int i = 0; i < ql[x].size(); ++ i) {
            ans[qid[x][i]] = (ql[x][i] <= maxl[qr[x][i]]);
        }
        memset(pre, 0, sizeof(pre));
        memset(maxl, 0, sizeof(maxl));
    }
    #define YNOI
    #ifdef YNOI
        for(i = 1; i <= m1; ++ i) {
            if(ans[i]) puts("yuno");
            else puts("yumi");
        }
    #endif // YNOI
    #ifndef YNOI
        for(i = 1; i <= m1; ++ i) {
            if(ans[i]) puts("hana");
            else puts("bi");
        }
    #endif
//    for(i = 1; i <= m1; ++ i) {
//        if(ans[i] == 1) puts("yuno");
//        else puts("yumi");
//    }
//    for(i = 1; i <= m1; ++ i) printf("%d\n", ans[i]);
    return 0;
}
```
