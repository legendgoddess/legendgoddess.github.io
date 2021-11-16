---
title: CF1404C Fixed Point Removal
date: 2021-11-16 14:31:00
tags:
  - 线段树
  - 数据结构
  - 贪心
categories:
  - CF题解
---

## [CF1404C Fixed Point Removal](https://www.luogu.com.cn/problem/CF1404C)

直接考虑每个数是否可能满足条件，显然对于 $a_i \le i$ 的情况才是可能的。而且发现对于 $\forall j > i$ 是不影响当前状态的。

我们考虑对于每个 $i$ 维护一下其距离条件还有多少位置，也就是 $i - a_i$。我们每一次将前面一个数删去，也就意味着后缀全部 $-1$。这个东西可以拿线段树简单维护。

发现对于 $r$ 只不过是对合法数范围的限制，我们直接维护一下区间合法的数的和即可。

之后我们只需要对于询问离线一下，从大到小按照 $l$ 加点即可。

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

const int maxn = 2e5 + 5;

int n, Q;
int ans[maxn];
const int inf = 0x3f3f3f3f;
namespace Segment {
    int mn[maxn << 2], tg[maxn << 2];
    struct Seg {
        #define ls (p << 1)
        #define rs (p << 1 | 1)
        #define mid ((l + r) >> 1)
        void build(int p,int l,int r) {
            mn[p] = inf, tg[p] = 0;
            if(l == r) return ;
            build(ls, l, mid), build(rs, mid + 1, r);
        }
        void pushup(int p) {
            mn[p] = min(mn[ls], mn[rs]);
        }
        void Add(int p,int c) {
            mn[p] += c, tg[p] += c;
//            printf("p = %d : %d %d\n", p, mn[p], tg[p]);
        }
        void pushdown(int p) {
            if(tg[p] == 0) return ;
            Add(ls, tg[p]), Add(rs, tg[p]);
            tg[p] = 0;
        }
        void change(int p,int l,int r,int ll,int rr) {
//            printf("(%d, %d) -> (%d, %d)\n", l, r, ll, rr);
            if(ll <= l && r <= rr) return Add(p, -1);
            pushdown(p);
            if(ll <= mid) change(ls, l, mid, ll, rr);
            if(mid < rr) change(rs, mid + 1, r, ll, rr);
            pushup(p);
        }
        void Insert(int p,int l,int r,int pos,int c) {
            if(l == r) return mn[p] = c, void();
            pushdown(p);
            if(pos <= mid) Insert(ls, l, mid, pos, c);
            else Insert(rs, mid + 1, r, pos, c);
            pushup(p);
        }
        int Ask(int p,int l,int r) {
            if(l == r) {
                if(mn[p] <= 0) return l;
                return 0;
            }
            pushdown(p);
            if(mn[ls] <= 0) return Ask(ls, l, mid);
            else if(mn[rs] <= 0) return Ask(rs, mid + 1, r);
            else return 0;
        }
        #undef ls
        #undef rs
        #undef mid
    }T;
}

using namespace Segment;

struct Seg1 {
    int t[maxn];
    int lowbit(int x) { return x & -x; }
    void add(int p,int c) { for(; p <= n; p += lowbit(p)) t[p] += c; }
    int ask(int p) { int res(0); for(; p > 0; p -= lowbit(p)) res += t[p]; return res; }
}Tas;
struct Ques {
    int l, r, id;
    int operator < (const Ques &z) const {
        return l > z.l;
    }
}q[maxn];
int a[maxn];
signed main() {
    int i, j;
    r1(n, Q);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i <= Q; ++ i) {
        r1(q[i].l, q[i].r);
        q[i].r = n - q[i].r, ++ q[i].l, q[i].id = i;
    }
    sort(q + 1, q + Q + 1);
    T.build(1, 1, n);
    int last = n + 1;
    for(i = 1; i <= Q; ++ i) {
        while(last > q[i].l && last > 0) {
            -- last; if(last < a[last]) continue;
            T.Insert(1, 1, n, last, last - a[last]);
        }
        int x;
        while(x = T.Ask(1, 1, n)) {
            Tas.add(x, 1);
            T.Insert(1, 1, n, x, inf);
            if(x + 1 <= n) T.change(1, 1, n, x + 1, n);
        }
//        printf("[%d, %d]\n", q[i].l, q[i].r);
        ans[q[i].id] = Tas.ask(q[i].r);
    }
    for(i = 1; i <= Q; ++ i) printf("%d\n", ans[i]);
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }

```

