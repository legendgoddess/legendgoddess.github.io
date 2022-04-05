---
title: "CF1299D Around the World 题解"
date: 2022-4-5 15:33:00
tags:
  - 图论
categories:
  - CF题解
---

[Problem - 1299D - Codeforces](https://codeforces.com/problemset/problem/1299/D)

> 切了，但是没有完全切。
> 
> 有的时候还要打表一下证明一下结论。
> 
> md 不用打表，直接算就行了，之前直接感觉就是 $2^x$，比较大。
> 
> 好吧，之前写过，严谨地说是抄过。现在能想到了还是挺好的。

打表得到线性基本质不同的个数为 $374$ 个。

考虑合并连通块就是线性基之间的转移，直接设 $f(i)$ 表示当前是第 $i$ 个线性基的方案数。

转移的时候保证线性无关就行了。

之后考虑三元环的情况：

> 因为没有重边所以没有二元环。

都是有 $2$ 条边连接节点 $1$，考虑分类讨论一下：

- 不选择，方案数为 $1$。

- 选择 $1$ 条边，随便选择即可方案数为 $2$。

- 选择两条边，加上当前线性基，方案数为 $1$。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
//		#define Fread
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
const int maxm = 374 + 5;
const int maxk = (1 << 16) + 5;
int n, m, head[maxn], cnt(1);
struct Edge {
    int to, next, w;
}edg[maxn << 1];
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}
constexpr int N = 4;
struct Seg {
    int a[5];
    Seg(void) { memset(a, 0, sizeof(a)); }
    int Insert(int x) {
        for(int i = N; i >= 0; -- i) if((x >> i) & 1) {
            if(a[i]) x ^= a[i];
            else {
                a[i] = x;
                for(int j = i - 1; j >= 0; -- j) if((a[i] >> j) & 1) a[i] ^= a[j];
                for(int j = N; j > i; -- j) if((a[j] >> i) & 1) a[j] ^= a[i];
                return true;
            }
        }
        return false;
    }
    int Hash() { return (a[0] | (a[1] << 1) | (a[2] << 3) | (a[3] << 6) | (a[4] << 10)); }
}fid[maxn], c[maxn];

int id[maxk];
int segtot(0);

void Dfs(Seg p) {
    if(id[p.Hash()]) return ;
    id[p.Hash()] = ++ segtot;
    fid[segtot] = p;
    for(int i = 1; i < 32; ++ i) {
        Seg np = p; if(np.Insert(i)) Dfs(np);
    }
}

int trans[maxm][maxm];

void init() {
    Seg tmp; Dfs(tmp);
    int i, j;
    for(i = 1; i <= segtot; ++ i) for(j = 1; j <= segtot; ++ j) {
        int fl(1);
        Seg tmp1 = fid[i];
        for(int k = N; k >= 0; -- k) if(fid[j].a[k] != 0) fl &= tmp1.Insert(fid[j].a[k]);
        if(fl) trans[i][j] = id[tmp1.Hash()];
    }
}

int dfn[maxn], dfntot(0), bl[maxn], ctot(0), dis[maxn], pd[maxn], st[maxn], edw[maxn], islp[maxn];

void dfs1(int p,int pre,Seg& now) {
    bl[p] = ctot, dfn[p] = ++ dfntot;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to, w = edg[i].w; if(to == pre || to == 1) continue;
        if(!bl[to]) dis[to] = dis[p] ^ w, dfs1(to, p, now);
        else if(dfn[to] < dfn[p]) pd[ctot] &= now.Insert(dis[p] ^ dis[to] ^ w);
    }
}

int f[maxn][maxm];

const int mod = 1e9 + 7;

void Add(int& x,const int& c) { x = (x + c) % mod; }

signed main() {
	int i, j;
    init();
//    printf("tot = %d\n", segtot);
    r1(n, m);
    for(i = 1; i <= m; ++ i) {
        int u, v, w; r1(u, v, w), add(u, v, w), add(v, u, w);
    }
    for(i = head[1];i;i = edg[i].next) {
        int to = edg[i].to, w = edg[i].w;
        if(!bl[to]) {
            ++ ctot, pd[ctot] = 1, edw[ctot] = w, st[ctot] = to, dfs1(to, 1, c[ctot]);
        }
        else {
            for(j = head[to];j;j = edg[j].next) {
                int nt = edg[j].to;
                if(st[bl[nt]] == nt) {
                    edw[bl[nt]] ^= w ^ edg[j].w;
                    islp[bl[nt]] = 1;
                }
            }
        }
    }
//    for(i = 1; i <= ctot; ++ i) printf("%d %d %d\n", i, islp[i], st[i]);
    f[0][id[0]] = 1;
    for(i = 1; i <= ctot; ++ i) {
        for(j = 1; j <= segtot; ++ j) f[i][j] = f[i - 1][j];
        if(!pd[i]) continue;
        if(!islp[i]) {
            int hs = id[c[i].Hash()];
//            printf("%d : %d\n", hs, trans[1][hs]);
            for(j = 1; j <= segtot; ++ j) Add(f[i][trans[j][hs]], f[i - 1][j]);
        }
        else {
            int hs = id[c[i].Hash()]; Seg tmp = c[i];
            int fl = tmp.Insert(edw[i]);
            int hs1 = id[tmp.Hash()];
//            printf("(%d, %d) = %d\n", hs, hs1, edw[i]);
            for(j = 1; j <= segtot; ++ j) {
                if(trans[j][hs]) Add(f[i][trans[j][hs]], 2ll * f[i - 1][j] % mod);
                if(fl && trans[j][hs1]) Add(f[i][trans[j][hs1]], f[i - 1][j]);
            }
        }
    }
    int ans(0);
    for(i = 1; i <= segtot; ++ i) Add(ans, f[ctot][i]);
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
