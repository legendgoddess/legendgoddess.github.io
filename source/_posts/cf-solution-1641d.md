---
title: "CF1641D Two Arrays 题解"
date: 2022-3-11 16:20:30
tags:
  - 数论
  - 数据结构
categories:
  - CF题解
---

[Problem - 1641D - Codeforces](https://codeforces.com/problemset/problem/1641/D)

事实上有很多种做法，比如随机化等等。

我们考虑如何判断两个集合是不同的从而推广到一个集合和多个集合的情况。

显然暴力判断复杂度是不能接受的，我们考虑通过枚举集合进行判断。

我们考虑一个式子 $\sum_{S_1 \subset A} \sum_{S_2 \subset B} [S_1 == S_2] $ 表示总共有多少个集合是相同的，也就是不妨设 $A \cap B = S$，那么本质上就是 $\sum_{i \ge 0} \binom{|S|} {i} $，我们灵光一闪发现 $\sum_{i \ge 0} \binom{n} {i} (-1) ^ i = [n == 0] $，我们可以通过这种方法来快速判断集合是否有交。

那么很简单，对于判断 $A$ 和若干个集合是否有交，我们可以考虑对于若干个集合建立一个扩展 $\tt Trie$ 树，之后直接暴力枚举 $A$ 就可以了，复杂度是 $2^m$。

之后我们考虑进行贪心，我们不妨将每个数组按照 $w$ 的大小进行排序，之后找到最靠左边的 $r$ 和 $l$ 进行匹配，之后进行移动 $r$ 显然 $l$ 只能向左移动才可能更优，复杂度 $O(n 2^m)$。

```cpp
#pragma GCC target("avx")
#pragma GCC optimize(2)
#pragma GCC optimize(3)
#pragma GCC optimize("Ofast")
#pragma GCC optimize("inline")
#pragma GCC optimize("-fgcse")
#pragma GCC optimize("-fgcse-lm")
#pragma GCC optimize("-fipa-sra")
#pragma GCC optimize("-ftree-pre")
#pragma GCC optimize("-ftree-vrp")
#pragma GCC optimize("-fpeephole2")
#pragma GCC optimize("-ffast-math")
#pragma GCC optimize("-fsched-spec")
#pragma GCC optimize("unroll-loops")
#pragma GCC optimize("-falign-jumps")
#pragma GCC optimize("-falign-loops")
#pragma GCC optimize("-falign-labels")
#pragma GCC optimize("-fdevirtualize")
#pragma GCC optimize("-fcaller-saves")
#pragma GCC optimize("-fcrossjumping")
#pragma GCC optimize("-fthread-jumps")
#pragma GCC optimize("-funroll-loops")
#pragma GCC optimize("-fwhole-program")
#pragma GCC optimize("-freorder-blocks")
#pragma GCC optimize("-fschedule-insns")
#pragma GCC optimize("inline-functions")
#pragma GCC optimize("-ftree-tail-merge")
#pragma GCC optimize("-fschedule-insns2")
#pragma GCC optimize("-fstrict-aliasing")
#pragma GCC optimize("-fstrict-overflow")
#pragma GCC optimize("-falign-functions")
#pragma GCC optimize("-fcse-skip-blocks")
#pragma GCC optimize("-fcse-follow-jumps")
#pragma GCC optimize("-fsched-interblock")
#pragma GCC optimize("-fpartial-inlining")
#pragma GCC optimize("no-stack-protector")
#pragma GCC optimize("-freorder-functions")
#pragma GCC optimize("-findirect-inlining")
#pragma GCC optimize("-fhoist-adjacent-loads")
#pragma GCC optimize("-frerun-cse-after-loop")
#pragma GCC optimize("inline-small-functions")
#pragma GCC optimize("-finline-small-functions")
#pragma GCC optimize("-ftree-switch-conversion")
#pragma GCC optimize("-foptimize-sibling-calls")
#pragma GCC optimize("-fexpensive-optimizations")
#pragma GCC optimize("-funsafe-loop-optimizations")
#pragma GCC optimize("inline-functions-called-once")
#pragma GCC optimize("-fdelete-null-pointer-checks")
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
		#define Fread
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

const int maxn = 2e5 + 5;

int n, m;

struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        // http://xorshift.di.unimi.it/splitmix64.c
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }

    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

struct Node {
    unordered_map<int, int, custom_hash> mp;
    int ct;
};

Node G[maxn * (1 << 5)];
struct Node1 {
    int x[6], w;
    int operator < (const Node1 &z) const {
        return w < z.w;
    }
    int& operator [] (const int &z) { return x[z]; }
    const int operator [] (const int &z) const { return x[z]; }
}a[maxn];

int get(const Node1& a, int dep, int ps) {
    if(dep == m + 1) return G[ps].ct;
    int res = get(a, dep + 1, ps);
    if(G[ps].mp.find(a[dep]) != G[ps].mp.end()) res -= get(a, dep + 1, G[ps].mp[a[dep]]);
    return res;
}

int tot(0), w[maxn];

void Insert(const Node1& a,int dep,int ps) {
    if(dep > m) return G[ps].ct ++, void();
    Insert(a, dep + 1, ps);
    if(G[ps].mp.find(a[dep]) == G[ps].mp.end()) G[ps].mp[a[dep]] = ++ tot;
    Insert(a, dep + 1, G[ps].mp[a[dep]]);
}

void Erase(const Node1& a,int dep,int ps) {
    if(dep > m) return G[ps].ct --, void();
    Erase(a, dep + 1, ps);
    Erase(a, dep + 1, G[ps].mp[a[dep]]);
}

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= n; ++ i) {
        for(j = 1; j <= m; ++ j) {
            r1(a[i][j]);
        }
        sort(a[i].x + 1, a[i].x + 1 + m);
        r1(a[i].w);
    }
    sort(a + 1, a + n + 1);
    int r = 1, l;
    while(r <= n && (!get(a[r], 1, 0))) Insert(a[r], 1, 0), ++ r;
    if(r > n) return puts("-1"), 0;
    l = r - 1;
    while(l > 0 && get(a[r], 1, 0)) Erase(a[l], 1, 0), -- l;
    int res = a[l + 1].w + a[r].w;
    for(int i = r + 1; i <= n; ++ i) {
        if(!l) break;
        if(get(a[i], 1, 0)) {
            while(l > 0 && get(a[i], 1, 0)) Erase(a[l], 1, 0), -- l;
            res = min(res, a[l + 1].w + a[i].w);
        }
    }
    printf("%d\n", res);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }

```




