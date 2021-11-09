---
title: CF1601C Optimal Insertion
date: 2021-11-09 20:20:00
tags:
  - 分治
  - 贪心
categories:
  - CF题解
---

<h3><center>CF1601C Optimal Insertion</center></h3>

> [CF1601C](C. Optimal Insertion)

我们不妨考虑让 $b$ 先进行排序从小到大进行插入。

考虑相邻两个位置的 $b$，不妨考虑为 $i, i + 1$。显然 $pos_i < pos_{i + 1}$，不然交换之后肯定可以得到更优的答案。

我们考虑既然有单调性，就可以进行分治。

我们考虑进行像 $\tt Dp$ 一样进行决策单调性分治，之后需要在 $O(n)$ 内算出 $b_{mid}$ 的最优位置。

我们不妨考虑钦定一个逆序对的基准值，之后每次移动 $b_{mid}$ 的位置都修改贡献，具体来说：

- $a_i < b_{mid}$ 就需要减少贡献。
- $a_i > b_{mid}$ 就需要增加贡献。

之后我们只要建出序列直接进行计算即可。

复杂度 $O((n + m) \log n)$。

```cpp
#include <bits/stdc++.h>
using namespace std;


namespace Legendgod {
namespace Read {
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

int a[maxn], b[maxn], n, m;

struct Seg {
    int t[maxn << 1];
    int lowbit(int x) { return x & -x; }
    void add(int p,int c) { for(; p > 0; p -= lowbit(p)) t[p] += c; }
    int ask(int p) { int res(0); for(; p <= n + m; p += lowbit(p)) res += t[p]; return res; }
    void Clear() { for(int i = 0; i <= n + m; ++ i) t[i] = 0; }
}T;

int pos[maxn << 1], c[maxn << 1];
int vis[maxn];

int tot(0);

void build() {
    for(int i = 1, j = 1; i <= n + 1; ++ i) {
        while(j <= m && pos[j] <= i) c[++ tot] = b[j], ++ j;
        if(i <= n) c[++ tot] = a[i];
    }
}

int64 getVal() {
    int64 res(0);
    for(int i = 1; i <= n + m; ++ i) {
        int tmp = T.ask(c[i] + 1);
//        printf("tmp = %d\n", tmp);
        res += T.ask(c[i] + 1);
        T.add(c[i], 1);
    }
    T.Clear();
    return res;
}
int pre[maxn], suf[maxn];
void Solve(int l,int r,int ll,int rr) {
    if(ll > rr) return ;
    int mid = (ll + rr) >> 1;
    int mx(n + 1), ps, sum(0);
    for(int i = l; i <= r; ++ i) {
        if(sum < mx) mx = sum, ps = i;
        sum += (a[i] > b[mid]) - (a[i] < b[mid]);
    }
    pos[mid] = ps;
    Solve(l, ps, ll, mid - 1), Solve(ps, r, mid + 1, rr);
}

void Solve() {
    int i, j; tot = 0;
    r1(n, m);
    static vector<int> vc; vc.clear();
    for(i = 1; i <= n; ++ i) r1(a[i]), vc.push_back(a[i]);
    for(i = 1; i <= m; ++ i) r1(b[i]), vc.push_back(b[i]);
    sort(vc.begin(), vc.end());
    vc.erase(unique(vc.begin(), vc.end()), vc.end());
    for(i = 1; i <= n; ++ i) a[i] = lower_bound(vc.begin(), vc.end(), a[i]) - vc.begin() + 1;
    for(i = 1; i <= m; ++ i) b[i] = lower_bound(vc.begin(), vc.end(), b[i]) - vc.begin() + 1;
    sort(b + 1, b + m + 1);
    Solve(1, n + 1, 1, m);
    build();
//    puts("st");
//    for(i = 1; i <= n + m; ++ i) printf("%d ", c[i]);
//    puts("ed");
    printf("%lld\n", getVal());
}

signed main() {
    int i, j, T;
    r1(T); while(T --) Solve();
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }
```

