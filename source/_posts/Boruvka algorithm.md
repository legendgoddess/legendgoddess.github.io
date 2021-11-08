---
title : Boruvka 算法
---

### [P3366 【模板】最小生成树](https://www.luogu.com.cn/problem/P3366)

这个算法就是考虑每一次合并 $n$ 个联通块，变成 $\frac{n}{2}$ 个。所以总共合并的次数是 $\log_2 n$ 次。

我们还是使用并查集来维护每一个联通块，这个算法的好处就是因为其合并次数是 $\log n$ 级别的，所以我们可以在里面进行 $O(n)$ 的处理。来处理一些不能直接进行最小生成树的情况。

比如说要维护两点 $\&$ 值为 $0$ 的图。我们直接使用 $\tt prim$ 复杂度会直接爆炸。但是我们用 $\tt Boruvka$ 可以每一次 $O(n)$ 级别处理各种联通块的信息，然后进行生成树。

---

对于模板题具体来说，我们对于每一个联通块维护一个最小的边权，边的另外一头是与其所属联通块不同的点。

这边注意每一次操作应该考虑的是所属联通块，然后这边有一个很好的性质 ~~可能也没有那么有性质~~。

$\color{red}\tt \text{在进行合并联通块之前，联通块的根是不会变的}$。

> 这样我们可以直接在预处理的时候存储每个集合的根，看起来这个性质不起眼，但是在题目中可以方便许多。

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
    int to, next, w;
}edg[maxn << 1];
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}

int fa[maxn], siz[maxn];

int getfa(int x) {
    return x == fa[x] ? x : fa[x] = getfa(fa[x]);
}

bool merge(int u,int v) {
    int s1 = getfa(u), s2 = getfa(v);
    if(s1 == s2) return false;
    if(siz[s1] > siz[s2]) swap(s1, s2);
    siz[s2] += siz[s1];
    fa[s1] = s2;
    return true;
}

#define pii pair<int, int>
#define id second
#define val first

pii mx[maxn];

int n, m;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, ans(0);
    r1(n, m);
    for(i = 1; i <= n; ++ i) fa[i] = i, siz[i] = 1;
    for(i = 1; i <= m; ++ i) {
        int u, v, w;
        r1(u, v, w);
        add(u, v, w), add(v, u, w);
    }

    int ct(n - 1);

    while(1) {
        for(i = 1; i <= n; ++ i) mx[i] = make_pair(2e9, -1);
        for(i = 1; i <= n; ++ i) {
            int s1 = getfa(i);
            for(int j = head[i];j;j = edg[j].next) {
                int to = edg[j].to, w = edg[j].w, s2 = getfa(to);
                if(s1 == s2) continue;
                if(mx[s1].val > w) mx[s1] = make_pair(w, s2);
            }
        }

        bool flag(0);

        for(i = 1; i <= n; ++ i) if(getfa(i) == i && mx[i].id != -1 && merge(i, mx[i].id)) {
            -- ct, ans += mx[i].val; flag = 1;
        }

        if(!flag) break;

    }

//    printf("ct = %d\n",ct);

    if(ct > 0) return puts("orz"), 0;

    printf("%d\n", ans);


	return 0;
}

```

