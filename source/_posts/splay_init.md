---
title: Splay 入门
date: 2021-11-12 11:45:00
tags:
  - 平衡树
  - 数据结构
categories:
  - 算法
---

<h3><center>Splay 入门</center></h3>

其实 $Splay$ 不能算作高速平衡树，但是事实上其代码非常好些只要记住一些细节。而且是平衡树中可以维护动态树的存在的，当然你也可以用 $FHQ-Treap$ 维护 $ETT$。

<h4><center>时间复杂度</center></h4>

我们必须了解一下每个平衡树维护平衡的方式，$Splay$ 是通过旋转，注意是双旋才可以保证复杂度。

我们将一个节点旋转到固定位置的操作称为 $splay$。

当然我们可能存在一次 $splay$ 是 $O(n)$ 的情况，根据势能分析可以得到复杂度是 $O(n \log n)$ 的。

因为是均摊而不是期望，所以我们不能对其进行可持久化操作。

<h4><center>splay 操作</center></h4>

我们需要先知道对于一条链我们会旋转成什么样子：

![IBcsa9.png](https://z3.ax1x.com/2021/11/12/IBcsa9.png)

最终会变成：

![IBccP1.png](https://z3.ax1x.com/2021/11/12/IBccP1.png)

我们直接对于当前操作进行模拟即可：

```cpp
void Rotate(int p) {
    int f = t[p].fa, ff = t[f].fa, k = pd(p);
    t[p].fa = ff;
    if(ff) t[ff].ch[pd(f)] = p;
    t[f].ch[k] = t[p].ch[!k], t[t[p].ch[!k]].fa = f;
    t[p].ch[!k] = f, t[f].fa = p;
    pushup(p), pushup(f);
}
```

对于 $\tt splay$ 操作，我们根据势能分析得到的结论，如果爷爷，父亲和自己在同一条链上，我们需要旋转父亲，不然就是旋转自己。之后再将自己旋转上去。

> 势能分析会放到文末。

```cpp
void Splay(int p,int goal = 0) {
    for(int f; (f = t[p].fa) != goal; Rotate(p)) if(t[f].fa != goal) {
        Rotate(pd(f) == pd(p) ? f : p);
    }
    rt = p;
}
```

---

其他操作的时候我们本质上就是应用 $\tt bst$ 的性质进行操作，唯一不同的是每次更新一个节点的信息的时候我们都需要 $\tt splay$ 来更新其祖先的所有信息。

对于删除的情况我们直接考虑旋转前驱后继进行删除即可。

----

[普通平衡树](https://www.luogu.com.cn/problem/P3369)

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

namespace Splay {
    struct Node {
        int val, siz, ts, fa, ch[2];
    }t[maxn];
    int tot(0), rt(0);
    struct Seg {
        #define ls t[p].ch[0]
        #define rs t[p].ch[1]
        void Prepare(int p,int c) { ls = rs = 0, t[p].fa = 0, t[p].ts = t[p].siz = 1, t[p].val = c; }
        void Clear(int p) { ls = rs = t[p].fa = t[p].ts = t[p].siz = t[p].val = 0; }
        void pushup(int p) { if(!p) return ; t[p].siz = t[p].ts; if(ls) t[p].siz += t[ls].siz; if(rs) t[p].siz += t[rs].siz; }
        int pd(int p) { return t[t[p].fa].ch[1] == p; } // is rson
        void Rotate(int p) {
            int f = t[p].fa, ff = t[f].fa, k = pd(p);
            t[p].fa = ff;
            if(ff) t[ff].ch[pd(f)] = p;
            t[f].ch[k] = t[p].ch[!k], t[t[p].ch[!k]].fa = f;
            t[p].ch[!k] = f, t[f].fa = p;
            pushup(p), pushup(f);
        }
        void Splay(int p,int goal = 0) {
            for(int f; (f = t[p].fa) != goal; Rotate(p)) if(t[f].fa != goal) {
                Rotate(pd(f) == pd(p) ? f : p);
            }
            rt = p;
        }

        void Insert(int c) {
            int p = rt, ff = 0;
            while(p && t[p].val != c) ff = p, p = t[p].ch[c > t[p].val];
            if(t[p].val == c) ++ t[p].ts, Splay(p);
            else {
                int p = ++ tot; Prepare(p, c);
                if(ff) t[p].fa = ff, t[ff].ch[c > t[ff].val] = p;
                Splay(p);
            }
        }


        void Find(int c) {
            int p = rt; if(!p) return ;
            while(t[p].val != c && t[p].ch[c > t[p].val]) p = t[p].ch[c > t[p].val];
            return Splay(p);
        }

        int Near(int c,int opt) { // 0 pre 1 suf
            Find(c); int p = rt;
            if((opt && t[p].val > c) || (!opt && t[p].val < c)) return p;
            for(p = t[p].ch[opt]; t[p].ch[!opt]; p = t[p].ch[!opt]);
            return p;
        }

        void Del(int c) {
            int pre = Near(c, 0), suf = Near(c, 1);
            Splay(pre, 0), Splay(suf, pre);
            int p = t[suf].ch[0];
//            printf("p = %d\n", p);
            if(t[p].ts > 1) -- t[p].ts, -- t[p].siz, Splay(p);
            else t[suf].ch[0] = 0, Clear(p), Splay(suf);
        }

        int Rnk(int c) { return Find(c), t[t[rt].ch[0]].siz; }

        int Kth(int K) {
            int p = rt;
            if(t[p].siz < K) return -1;
            while(1) {
                if(ls && t[ls].siz >= K) p = ls;
                else {
                    K -= t[p].ts + (ls ? t[ls].siz : 0);
                    if(K <= 0) return t[p].val;
                    p = rs;
                }
            }
        }

        #undef ls
        #undef rs
    }T;
}

using namespace Splay;

int n;
const int inf = 0x3f3f3f3f;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    T.Insert(- inf), T.Insert(inf);
    for(i = 1; i <= n; ++ i) {
        int opt, c; r1(opt, c);
        switch(opt) {
            case 1: T.Insert(c); break;
            case 2: T.Del(c); break;
            case 3: printf("%d\n", T.Rnk(c)); break;
            case 4: printf("%d\n", T.Kth(c + 1)); break;
            case 5: printf("%d\n", t[T.Near(c, 0)].val); break;
            case 6: printf("%d\n", t[T.Near(c, 1)].val); break;
            default: break;
        }
    }
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }
```
