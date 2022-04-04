---
title: "CF1499G Graph Coloring 题解"
date: 2022-4-4 18:30:00
tags:
  - 图论
  - 二分图
categories:
  - CF题解
---

[Problem - 1499G - Codeforces](https://codeforces.com/problemset/problem/1499/G)

> 完蛋一点不会。

考虑没有询问的时候是考虑对于度数为奇数的点连接一个虚点，之后跑欧拉回路钦定入边为红色，出边为蓝色即可。

之后考虑虚拟点不是很好搞，所以考虑删除虚拟点那么原图就是一堆环和路径，考虑加入一条边的情况：

- 边的端点不是路径端点，直接作为新的路径。

- 边的一段是路径端点，合并路径。

- 边的两端都是路径，要么合并两条之前不同的路径，要么之前的一个路径变成了环。

我们考虑欧拉回路本质上就是对边进行黑白染色，我们考虑染色的问题只在最后一种情况上出现，事实上我们只要翻转奇偶性就行了。

所以考虑使用并查集来维护即可，注意要维护红边的哈希值的和，但是有翻转操作所以两边都要维护。

可能最近生病了（借口），一开始写的时候一点都不清晰，在看题解。

但事实上我们只需要考虑每个点正好断掉的路径，然后记录整条路径当前是否被翻转过，显然如果中间连接一条边必定满足该边和左右两条路径颜色都不同，且左右两条颜色相同。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
    namespace Read {
//        #define Fread
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

const int maxn = 4e5 + 5;
const int mod = 998244353;

int n, n1, n2, m, fa[maxn], rev[maxn], v[maxn][2], pw[maxn], to[maxn];
long long res;

int getfa(int x) {
    if(fa[x] == x) return x;
    if(fa[fa[x]] == fa[x]) return fa[x];
    int rs = getfa(fa[x]);
    rev[x] ^= rev[fa[x]];
    return fa[x] = rs;
}

int col(int x) {
    if(x == fa[x]) return rev[x];
    int rs = getfa(x);
    return rev[x] ^ rev[rs];
}

template <typename T>
void Del(T& x,const int& c) { x = (x - c + mod) % mod; }
template <typename T>
void Add(T& x,const int& c) { x = (x + c) % mod; }

void Rev(int x) {
    x = getfa(x);
    Del(res, v[x][!rev[x]]);
    rev[x] ^= 1;
    Add(res, v[x][!rev[x]]);
}

void merge(int x,int y) {
    x = getfa(x), y = getfa(y);
    if(x == y) return ;
    Add(v[y][rev[y]], v[x][rev[x]]);
    Add(v[y][!rev[y]], v[x][!rev[x]]);
    fa[x] = y;
    rev[x] ^= rev[y];
}

void Link(int x,int y,int id) {
    fa[id] = id, v[id][0] = pw[id];
    if(!to[x] && !to[y]) return to[x] = to[y] = id, void();
    if(!to[x]) swap(x, y);
    if(!to[y]) {
        if(!col(to[x])) Rev(id);
        merge(to[x], id), to[x] = 0, to[y] = id;
        return ;
    }
    if(col(to[x]) != col(to[y])) Rev(to[x]);
    if(!col(to[x])) Rev(id);
    merge(to[x], id), merge(id, to[y]);
    to[x] = to[y] = 0;
}

signed main() {
    int i, j;
    r1(n1, n2, m), n = n1 + n2;
    pw[0] = 1;
    for(i = 1; i < maxn; ++ i) pw[i] = 2ll * pw[i - 1] % mod;
    for(i = 1; i <= m; ++ i) {
        int u, v; r1(u, v), Link(u, v + n1, i);
    }
    int Q; r1(Q);
    while(Q --) {
        int opt, u, v;
        r1(opt);
        if(opt == 1) {
            r1(u, v);
            Link(u, v + n1, ++ m);
            printf("%lld\n", res);
        }
        else {
            int ct(0);
            for(i = 1; i <= m; ++ i) if(col(i)) ++ ct;
            printf("%d ", ct);
            for(i = 1; i <= m; ++ i) if(col(i)) printf("%d ", i);
            puts("");
        }
        fflush(stdout);
    }
    return 0;
}

}


signed main() { return Legendgod::main(), 0; }//
```
