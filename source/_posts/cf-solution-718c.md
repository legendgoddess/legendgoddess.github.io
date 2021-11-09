---
title: CF718C Sasha and Array
date: 2021-09-23 10:27:58
tags: 
  - Dp
  - 数据结构
  - 矩阵
categories: 
  - CF题解
---

<h3><center>CF718C Sasha and Array</center></h3>

> [CF718C Sasha and Array](https://codeforces.ml/problemset/problem/718/C)

**重点：**

> 矩阵乘法 $a\times(b + c) = ab + ac$。
>
> 而且矩阵乘法是有结合律的：$a \times b \times c = a\times (b\times c)$
>
> 但是 $\color{red}\text{没有}$ 交换律！！

发现这个下标的信息不是很好维护我们考虑将其放到矩阵里面进行维护。

也就是说我们需要维护一个权值和还有一个转移的矩阵。

对于一个区间加的操作对于整体区间都可以乘上一个转移矩阵。

我们考虑计算答案的时候直接维护一个答案矩阵即可。

具体来说不妨设初始矩阵为：
$$
\left[
\begin{matrix}
f_{i - 1}, f_i
\end{matrix}
\right]
$$
那么转移矩阵就是：
$$
\left[
\begin{matrix}
0 & 1 \\
1 & 1
\end{matrix}
\right]
$$
我们分别维护这两个量，每次下传标记的时候更新即可。

---

题外话：

我想的时候想到了矩阵，但是不知道为什么就是搞不懂要怎么维护。思路历程是这样的：我们考虑维护整个答案的矩阵，如果整体修改很好办，但是如果部分修改呢？

> 其实这个就和普通的线段树维护一样，只需要考虑如何合并左右两个节点的信息而已。
>
> 对于矩阵的信息我们可以直接加，也就是 $ca + cb$ 的形式。
>
> 但是如果区间修改的话，一开始没有想到好的办法，其实本质上就是 $c \times (a + b) = ca + cb$。所以直接对于之前的答案矩阵进行乘法即可。

---

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
#define Getmod

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

#ifdef Getmod
const int mod  = 1e9 + 7;
template <int mod>
struct typemod {
    int z;
    typemod(int a = 0) : z(a) {}
    inline int inc(int a,int b) const {return a += b - mod, a + ((a >> 31) & mod);}
    inline int dec(int a,int b) const {return a -= b, a + ((a >> 31) & mod);}
    inline int mul(int a,int b) const {return 1ll * a * b % mod;}
    typemod<mod> operator + (const typemod<mod> &x) const {return typemod(inc(z, x.z));}
    typemod<mod> operator - (const typemod<mod> &x) const {return typemod(dec(z, x.z));}
    typemod<mod> operator * (const typemod<mod> &x) const {return typemod(mul(z, x.z));}
    typemod<mod>& operator += (const typemod<mod> &x) {*this = *this + x; return *this;}
    typemod<mod>& operator -= (const typemod<mod> &x) {*this = *this - x; return *this;}
    typemod<mod>& operator *= (const typemod<mod> &x) {*this = *this * x; return *this;}
    int operator == (const typemod<mod> &x) const {return x.z == z;}
    int operator != (const typemod<mod> &x) const {return x.z != z;}
};
typedef typemod<mod> Tm;
#endif

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

struct Matrix {
    Tm a[2][2];
    Matrix(void) { a[0][0] = a[0][1] = a[1][0] = a[1][1] = 0; }

    void init() {
        a[0][0] = 0;
        a[0][1] = a[1][0] = a[1][1] = 1;
//        a[0][0] = a[0][1] = a[1][0] = 1;
//        a[1][1] = 0;
    }

    Matrix operator * (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < 2; ++ i) {
            for(int j = 0; j < 2; ++ j) {
                for(int k = 0; k < 2; ++ k) {
                    res.a[i][j] += a[i][k] * z.a[k][j];
                }
            }
        }
        return res;
    }
    Matrix operator + (const Matrix &z) const {
        Matrix res;
        for(int i = 0; i < 2; ++ i) {
            for(int j = 0; j < 2; ++ j) {
                res.a[i][j] = a[i][j] + z.a[i][j];
            }
        }
        return res;
    }

    void print() {
        for(int i = 0; i < 2; ++ i) {
            for(int j = 0; j < 2; ++ j) {
                printf("(%d, %d) = %d\n", i, j, a[i][j].z);
            }
        }
        puts("\n ---------- \n");
    }

}Fir, tmp;

struct Node {
    Matrix tag, mt;
    bool empty() {
        return (bool)(tag.a[0][0] == 1 && tag.a[1][1] == 1 && tag.a[0][1] + tag.a[1][0] == 0);
    }
}t[maxn << 2];

Matrix ksm(Matrix a,int mi) {
    Matrix res;
    res.a[0][0] = res.a[1][1] = 1;
    while(mi) {
        if(mi & 1) res = res * a;
        mi >>= 1;
        a = a * a;
    }
    return res;
}

int v[maxn];

struct Seg {
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid ((l + r) >> 1)

    void pushup(int p) {
        t[p].mt = t[ls].mt + t[rs].mt;
    }

    void build(int p,int l,int r) {
        t[p].tag.a[0][0] = t[p].tag.a[1][1] = 1;
        t[p].tag.a[0][1] = t[p].tag.a[1][0] = 0;
        if(l == r) return t[p].mt = Fir * ksm(tmp, v[l] - 1), void();
        build(ls, l, mid), build(rs, mid + 1, r);
        pushup(p);
    }

    void Add(int p,const Matrix& c) {
        t[p].mt = t[p].mt * c;
        t[p].tag = t[p].tag * c;
    }

    void pushdown(int p) {
        if(t[p].empty()) return ;
        Add(ls, t[p].tag), Add(rs, t[p].tag);
        t[p].tag.a[0][0] = t[p].tag.a[1][1] = 1;
        t[p].tag.a[0][1] = t[p].tag.a[1][0] = 0;
    }

    void change(int p,int l,int r,int ll,int rr, const Matrix& c) {
        if(ll <= l && r <= rr) return Add(p, c);
        pushdown(p);
        if(ll <= mid) change(ls, l, mid, ll, rr, c);
        if(mid < rr) change(rs, mid + 1, r, ll, rr, c);
        pushup(p);
    }

    Matrix Ask(int p,int l,int r,int ll,int rr) {
        if(ll <= l && r <= rr) return t[p].mt;
        pushdown(p);
        Matrix res;
        if(ll <= mid) res = res + Ask(ls, l, mid, ll, rr);
        if(mid < rr) res = res + Ask(rs, mid + 1, r, ll, rr);
        pushup(p);
        return res;
    }

}T;

int n, m;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    Fir.a[0][0] = Fir.a[0][1] = 1;
    tmp.init();
//    Matrix c = Fir * ksm(tmp, 0);
//    c.print();
    r1(n, m);
    for(i = 1; i <= n; ++ i) r1(v[i]);
    T.build(1, 1, n);
    while(m --) {
        int opt, l, r, x;
        r1(opt, l, r);
        if(opt == 1) {
            r1(x);
            T.change(1, 1, n, l, r, ksm(tmp, x));
        }
        else {
            printf("%d\n", T.Ask(1, 1, n, l, r).a[0][0].z);
        }
    }
	return 0;
}

/*
5 3
1 1 1 1 1
2 1 1
1 1 5 2
2 1 1
*/
```




