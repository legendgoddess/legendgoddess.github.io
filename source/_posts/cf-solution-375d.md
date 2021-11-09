---
title: CF375D Tree and Querie
date: 2021-08-24 15:34:40
tags:
  - 数据结构
categories:
  - CF题解
---

### [CF375D Tree and Queries](https://codeforces.com/problemset/problem/375/D)

线段树合并。

需要记录两颗线段树，其中一棵表示当前节点的颜色数量，另外一个表示出现次数为 $i$ 的颜色个数。

前面一棵直接进行线段树合并即可。后面的那棵能线段树合并的条件当且仅当有一种颜色只在一个子树中出现，我们可以直接对于这种情况进行合并。不然我们需要修改颜色，也就是暴力修改。

暴力修改的次数最多是 $O(n)$ 次。复杂度正确。

**$Tricks：$**

我们先将可以进行合并的节点进行合并，之后再暴力修改。具体来说就是先统计哪个颜色是不符合标准的，之后将包含这个颜色的节点的所有贡献减去。之后直接线段树合并即可。

我们可以在线段树合并到叶子节点的时候记录同一个颜色在几个子树中出现过。

**注意：** 在线段树合并结束的时候不要忘记将权值合并。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
//#define Getmod

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

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;

int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

struct Node {
    int l, r, val;
}t[maxn * 40];

int tot(0);
int tag[maxn];
vector<int> V, C;
const int N = 1e5;
struct Seg {
    #define ls t[p].l
    #define rs t[p].r
    #define mid ((l + r) >> 1)
    void Insert(int &p,int l,int r,int pos,int c) {
        if(!p) p = ++ tot;
        t[p].val += c;
        if(l == r) return ;
        if(pos <= mid) Insert(ls, l, mid, pos, c);
        else Insert(rs, mid + 1, r, pos, c);
    }
    int merge(int u,int v,int l,int r) {
        if(!u || !v) return u + v;
        if(l == r) {
            if(!tag[l] && t[u].val > 0) {
                tag[l] = 1;
                C.push_back(l);
                V.push_back(t[u].val);
            }
            if(t[v].val > 0) V.push_back(t[v].val);
            t[u].val += t[v].val;
            return u;
        }
        t[u].val += t[v].val;
        t[u].l = merge(t[u].l, t[v].l, l, mid);
        t[u].r = merge(t[u].r, t[v].r, mid + 1, r);
        return u;
    }
    int ask(int p,int l,int r,int ll,int rr) {
        if(!p) return 0;
        if(ll <= l && r <= rr) return t[p].val;
        int res(0);
        if(ll <= mid) res += ask(ls, l, mid, ll, rr);
        if(mid < rr) res += ask(rs, mid + 1, r, ll, rr);
        return res;
    }
}T;

int rt1[maxn], rt2[maxn];

struct Query {
    int k, id;
};

int ans[maxn], a[maxn];
vector<Query> v[maxn];

void dfs(int p,int pre) {
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        dfs(to, p);
    }
    V.clear(), C.clear();
    T.Insert(rt1[p], 1, N, a[p], 1);
    T.Insert(rt2[p], 1, N, 1, 1);
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        rt1[p] = T.merge(rt1[p], rt1[to], 1, N);
    }
    for(int i = 0; i < V.size(); ++ i) T.Insert(rt2[p], 1, N, V[i], -1); // 减去多余线段树合并的贡献
    for(int i = 0; i < C.size(); ++ i) T.Insert(rt2[p], 1, N, T.ask(rt1[p], 1, N, C[i], C[i]), 1); // 统计所有颜色
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        rt2[p] = T.merge(rt2[p], rt2[to], 1, N);
    }
    for(auto x : v[p]) ans[x.id] = T.ask(rt2[p], 1, N, x.k, N);
//    printf("valall = %d\n", T.ask(rt2[p], 1, N, 1, N));
    for(int i = 0; i < C.size(); ++ i) tag[C[i]] = 0;
}

int n, m;
/*
4 1
1 2 3 4
1 2
2 3
3 4
1 1
*/
signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) r1(a[i]);
    for(i = 1; i < n; ++ i) {
        int u, v;
        r1(u, v);
        add(u, v), add(v, u);
    }
    for(i = 1; i <= m; ++ i) {
        int u, k;
        r1(u, k);
        v[u].push_back((Query) {k, i});
    }
    dfs(1, 0);
    for(i = 1; i <= m; ++ i) printf("%d\n", ans[i]);
	return 0;
}
```




