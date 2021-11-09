---
title: CF762F Tree nesting
date: 2021-09-01 21:52:44
tags:
  - Dp
  - 图论
categories:
  - CF题解
---


### [CF762F](http://codeforces.com/problemset/problem/762/F)

> 其实这个题也不是很复杂。

首先题目背景里说的是一个联通子图！！！

发现数据范围其实不是很大，我们不妨钦定一个节点为根$S$ 的节点。那么本质上对于 $S$ 中的每一个节点我们都需要对 $T$ 进行一次匹配。

显然因为是联通子图是不可能进行树哈希的，我们考虑进行树形 $Dp$。

不妨考虑设 $f(u, v, S)$ 表示对于 $u \in S, v \in T$ 的情况，已经匹配了 $T$ 中集合 $S$ 的方案数。

但是题目中说明的是无根树，如果再对于 $T$ 钦定一个根的话会产生有些形态没有计算的情况。

所以我们不得不考虑重复计算的情况，也就是一个情况能计算几次。本质就是一个和 $T$ 本质相同的树会被计算几次。

这两个相除即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

#define Fread
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
const int maxn = 1e3 + 5;
const int maxm = 12 + 5;

const int M = 13;

Tm ksm(Tm x,int mi) {
    Tm res(1);
    while(mi) {
        if(mi & 1) res *= x;
        mi >>= 1;
        x *= x;
    }
    return res;
}

Tm Inv(Tm x) {
    return ksm(x, mod - 2);
}

struct Tree : vector<vector<int>> { // 挺实用的写法
    void add(int u,int v) {
        this->at(u).push_back(v), this->at(v).push_back(u);
    }
};

Tree S, T;

void init(Tree &A) {
    int n; r1(n); A.resize(n);
    for(int x, y, i = 1; i < n; ++ i) {
        r1(x, y), A.add(x - 1, y - 1);
    }
}

int lg[1 << M];

void Del(vector<int> & T, int rt) {
    for(auto it = T.begin(); it != T.end(); ++ it) {
        while(*it == rt) {
            it = T.erase(it); // 返回的是后面一个迭代器
            if(it == T.end()) return ;
        }
    }
}

Tree Build(Tree A, int num) { // 首先预处理出树的形态，也就是变成外向树
    static queue<int> q; while(!q.empty()) q.pop();
    q.push(num);
    while(!q.empty()) {
        int u = q.front(); q.pop();
        for(auto v : A[u]) {
            Del(A[v], u);
            q.push(v);
        }
    }
    return A;
}
vector<Tm> dp[maxn][maxm];

Tm dfs(const Tree &A,const Tree &B, int s,int t) {
    vector<Tm>& res = dp[s][t];
    if(!res.empty()) return res.back();
    int z = (1 << B[t].size());
    res.resize(z);
    res[0] = 1;
    for(auto v : A[s]) {
        for(int i = z - 1; i >= 0; -- i) {
            auto lowbit = [&] (int x) {
                return x & -x;
            };
            for(int j = i; j > 0; j -= lowbit(j)) {
                int ct = lg[lowbit(j)];
                if(res[i ^ (1 << ct)].z)
                    res[i] += res[i ^ (1 << ct)] * dfs(A, B, v, B[t][ct]);
            }
        }
    }
    return res.back();
}

Tm Match(const Tree& A, const Tree& B) {
    if(A.size() < B.size()) return 0;
    if(B.size() <= 1) return A.size();
    auto Clear = [&] () {
        for(int i = 0; i < A.size(); ++ i) {
            for(int j = 0; j < B.size(); ++ j)
                dp[i][j].clear();
        }
    };
    Tm ans(0);
    Tree nA = Build(A, 0);
    for(int j = 0; j < B.size(); ++ j) {
        Tree nB = Build(B, j);
        Clear();
        for(int i = 0; i < A.size(); ++ i)
            ans += dfs(nA, nB, i, j);
    }
    return ans;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    init(S), init(T);
    for(int i = 2; i < (1 << M); ++ i) lg[i] = lg[i >> 1] + 1;
    Tm ans = Match(S, T);
    ans *= Inv(Match(T, T));
    printf("%d\n", ans.z);
	return 0;
}
```



