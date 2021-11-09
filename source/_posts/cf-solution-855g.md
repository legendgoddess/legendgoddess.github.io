---
title: CF855G Harry Vs Voldemort
date: 2021-09-02 21:45:26
tags:
  - 数论
  - Dp
categories:
  - CF题解
---

### [CF855G Harry Vs Voldemort](https://www.luogu.com.cn/problem/CF855G)

> 根据 $\tt\color{black}{h}\color{red} ater$ 的话，这个东西放到现在有 $2800$。
>
> 显然恶评。

就是对于一个 $3$ 元组，我们发现本质就是树上两条路径合并的问题，那么我们像淀粉质一样将每个点作为 $\tt Lca$ 计算答案即可。

那么一开始的答案就是 $ans_u = (n - 1) \times (n - 1) - \sum_{v \in son_u} siz_v \times siz_v - (n - siz_u) \times (n - siz_u)$。

> 本质上就是考虑不取当前的点，任意的点对，为了方便我们就是考虑到 $(i, i)$ 这样的点对，反正也会减去的。

之后考虑加入一条边就是增加一个环，那么我们对于环中的每一个点的答案本质是相同的，我们对于一个环可以一起计算，不妨设其深度最浅的节点为 $u$，整个环的儿子集合为 $son$，整个环的元素个数是 $s$。

- 都在环上 $s \times (s - 1) \times (s - 2)$。
- 一个在环上 $2 \times (s - 1) \times (n - s)$ 钦定一个中间节点，然后两倍是因为两个点可以交换。
- 都不在环上 $(n - s) \times (n - s) - \sum_{v \in son} siz_v \times siz_v - siz_u \times siz_u$。

最后只要乘上元素的个数就是整个环的答案。

新增加一条边的时候，那么肯定是形成一个大的双联通分量，那么中间的每个点都需要被合并。我们直接对于每一个双联通分量暴力跳父亲合并即可。

复杂度是 $O(n \log n)$，如果并查集使用路径压缩和按秩合并的话是 $O(n \alpha(n))$。

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

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, n;
    struct DSU : vector<int>{
        DSU(int n = 0) { this->resize(n + 1); }
        int getfa(int x) {
            return x == this->at(x) ? x : this->at(x) = getfa(this->at(x));
        }
    };
    r1(n);
    vector<vector<int>> vc(n + 1);
    auto add = [&] (int u,int v) -> void{
        vc[u].emplace_back(v);
    };
    for(i = 1; i < n; ++ i) {
        int u, v; r1(u, v);
        add(u, v), add(v, u);
    }
    vector<int> dep(n + 1, 0), siz(n + 1, 0), rp(n + 1, 0), fa(n + 1, 0);
    vector<long long> val(n + 1, 0ll);
    function<void(const int&, int)> dfs;
    dfs = [&](const int& p,int pre) -> void{
        rp[p] = siz[p] = 1;
        fa[p] = pre;
        for(auto v : vc[p]) if(v != pre) {
            dep[v] = dep[p] + 1;
            dfs(v, p);
            siz[p] += siz[v];
            val[p] += 1ll * siz[v] * siz[v];
        }
    };
    dfs(1, 0);
    DSU T(n);
    for(i = 1; i <= n; ++ i) T[i] = i;
    long long ans(0);

    auto Bef = [=, &ans]() -> void{
        for(int i = 1; i <= n; ++ i) ans += 1ll * (n - 1) * (n - 1) - val[i] - 1ll * (n - siz[i]) * (n - siz[i]);
    };
    Bef();
    auto Calc = [&] (const int &u, const int &opt) -> void{
        assert(u == T.getfa(u));
        long long s = rp[u];
        ans += opt * s * (s - 1) * (s - 2);
        ans += 2 * opt * s * (s - 1) * (n - s);
//        printf("s = %d\n", s);
        ans += opt * s * ( 1ll * (n - s) * (n - s) - val[u] - 1ll * (n - siz[u]) * (n - siz[u]) );
    };
    auto Merge = [&] (const int &u, const int &v) -> void{ // dep[u] < dep[v]
//        puts("SSSS");
//        printf("u = %d, v = %d\n", u, v);
//        printf("u = %d, v = %d\n", dep[u], dep[v]);
//        assert(T.getfa(u) == u && T.getfa(v) == v && dep[u] < dep[v]);
        assert(T.getfa(u) == u && T.getfa(v) == v);
        Calc(u, -1), Calc(v, -1);
        val[u] -= 1ll * siz[v] * siz[v]; // 合并肯定是直接连接的
        val[u] += val[v];
        rp[u] += rp[v];
//        printf("ans = %lld\n", ans);
        Calc(u, 1);
        auto merge = [&] (int x,int y) -> void{
            x = T.getfa(x), y = T.getfa(y);
//            printf("x = %d, y = %d\n", x, y);
            if(x != y) T[y] = x;
        };
        merge(u, v);
    };
    printf("%lld\n", ans);
    int m; r1(m);
    while(m --) {
        int u, v; r1(u, v);
        while(T.getfa(u) != T.getfa(v)) {
            if(dep[T.getfa(u)] < dep[T.getfa(v)]) swap(u, v);
//            printf("u = %d, v = %d\n", u, T.getfa(fa[u]));
            u = T.getfa(u);
            Merge(T.getfa(fa[u]), u);
        }
        printf("%lld\n", ans);
    }
	return 0;
}
```

先说一下抱歉，这里的码风比较奇怪，因为这题比较简单那么久顺便练习一下 $\tt c++14$ 的语法和知识点。

但是好处是每一个函数调用时比较明确的。


