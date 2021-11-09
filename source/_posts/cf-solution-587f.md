---
title: CF587F Duff is Mad 题解
date: 2021-08-23 21:21:03
tags:
  - 分治
  - 数据结构
categories:
  - CF题解
---

### [CF587F Duff is Mad](https://www.luogu.com.cn/problem/CF587F)

暴力就是每个串都在自动机上跑一下。

稍微优化一下就是之前跑过的就不跑了。

> 在跑的时候把所有的答案都记录下来。

之后考虑进行根号分治。

如果说当前串的长度 $> \sqrt n$ 那么出现次数肯定 $< \sqrt n$。我们直接进行暴力跑，是可以接受的。

对于长度 $< \sqrt n$ 的串，我们考虑建立 $fail$ 树。之后从根节点开始遍历，对于每一个结束标记都放到主席树里面去。

对于查询的时候从上到下走自动机上的节点加上对应的贡献即可。

使用主席树维护每一个节点的前缀和即可。

> 省空间。

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
const int maxn = 1e5 + 5;
const int maxm = maxn << 1;
int tot(0);
int t[maxn][26], fail[maxn];
string c[maxn];
int len[maxn];
int point[maxn];
vector<int> pt[maxn];
int totlen(0);
void Insert(int i) {
    cin >> c[i];
    len[i] = c[i].size(), totlen += len[i];
    int now(0);
    for(int j = 0; j < len[i]; ++ j) {
        int x = (c[i][j] - 'a');
        if(!t[now][x]) t[now][x] = ++ tot;
        now = t[now][x];
    }
    pt[now].push_back(i); point[i] = now;
}

void build() {
    static queue<int> q;
    while(!q.empty()) q.pop();
    for(int i = 0; i < 26; ++ i) if(t[0][i]) q.push(t[0][i]), fail[t[0][i]] = 0;
    while(!q.empty()) {
        int u = q.front(); q.pop();
        for(int i = 0; i < 26; ++ i) {
            if(t[u][i]) fail[t[u][i]] = t[fail[u]][i], q.push(t[u][i]);
            else t[u][i] = t[fail[u]][i];
        }
    }
}

int head[maxn], cnt;
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
//    printf("(%d, %d)\n", u, v);
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

struct Query {
    int l, r, id, k, opt;
    int operator < (const Query &z) const {
        return opt == z.opt ? k < z.k : opt > z.opt;
    }
}q[maxn];

long long ans[maxn];
struct Node {
    int l, r;
    long long val;
}tr[maxn * 40];
int rt[maxn], ct(0);
struct Seg {
    #define ls tr[p].l
    #define rs tr[p].r
    #define mid ((l + r) >> 1)
    void Insert(int &p, int pre,int l,int r,int pos) {
        p = ++ ct;
        tr[p] = tr[pre];
        ++ tr[p].val;
        if(l == r) return ;
        if(pos <= mid) Insert(ls, tr[pre].l, l, mid, pos);
        else Insert(rs, tr[pre].r, mid + 1, r, pos);
    }
    int ask(int p,int l,int r,int pos) {
        if(!pos) return 0;
        if(l == r) return tr[p].val;
        if(pos <= mid) return ask(ls, l, mid, pos);
        else return tr[ls].val + ask(rs, mid + 1, r, pos);
    }
}T;

int n, m;

void dfs(int p) {
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; rt[to] = rt[p];
        for(int j = 0; j < pt[to].size(); ++ j) T.Insert(rt[to], rt[to], 1, n, pt[to][j]);
        dfs(to);
    }
}

long long sum[maxn];

void Dfs(int p) {
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        Dfs(to); sum[p] += sum[to];
    }
}

long long pre[maxn];

void Solve(int i) {
    for(int j = 0, now = 0; j < len[i]; ++ j) {
        int x = c[i][j] - 'a';
        now = t[now][x];
        ++ sum[now];
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) Insert(i);
    build();
    totlen = sqrt(totlen);
    for(i = 1; i <= m; ++ i) {
        int l, r, k;
        r1(l, r, k);
        q[i] = (Query) {l, r, i, k, (bool)(len[k] > totlen)};
    }
    sort(q + 1, q + m + 1);
    for(i = 1; i <= tot; ++ i) add(fail[i], i);
    dfs(0);
    for(i = 1; i <= m; i = j + 1) {
        if(q[i].opt == 0) break;
        j = i;
        for(; j < m && q[j + 1].k == q[i].k && q[j + 1].opt == 1; ++ j) ;
        memset(sum, 0, sizeof(sum));
        Solve(q[i].k), Dfs(0);
        for(int k = 1; k <= n; ++ k) pre[k] = pre[k - 1] + sum[point[k]];
        for(int k = i; k <= j; ++ k) ans[q[k].id] = pre[q[k].r] - pre[q[k].l - 1];
    }
    for(; i <= m; ++ i) {
        for(int j = 0, now = 0; j < len[q[i].k]; ++ j) {
            int x = c[q[i].k][j] - 'a';
            now = t[now][x];
            ans[q[i].id] += T.ask(rt[now], 1, n, q[i].r) - T.ask(rt[now], 1, n, q[i].l - 1);
        }
    }
    for(i = 1; i <= m; ++ i) printf("%lld\n", ans[i]);
	return 0;
}
```




