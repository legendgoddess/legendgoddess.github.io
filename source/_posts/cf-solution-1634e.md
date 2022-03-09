---
title: CF1634E Fair Share 题解
date: 2022-3-9 10:26:00
tags:
  - 图论
  - 欧拉回路
categories:
  - CF题解
---

[Problem - 1634E - Codeforces](https://codeforces.com/problemset/problem/1634/E)

注意都是偶数的性质，我们尝试考虑建立欧拉回路。

因为欧拉回路上每个节点的入度等于出度，我们直接将限制建立成点即可。

也就是对于每个数组建一个点，每种颜色建立一个点，如果数组 $i$ 中有颜色 $j$ 那么我们就建立无向边 $(i, j)$，我们将这个位置的标号当做边权，不妨钦定 $i \to j$ 的边我们将其设为 `L`，否则就是 `R`。

我们可以发现对于同一行连出去的边肯定都是在当前数组内的，所以直接开桶统计答案即可。

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

const int maxn = 4e5 + 5;

int n, m;
vector<int> a[maxn];
int tmp[maxn], tot;

int head[maxn], cnt(1);
struct Edge {
    int to, next, ps;
}edg[maxn << 1];
int vise[maxn << 1];

void add(int u,int v,int ps) {
    edg[++ cnt] = (Edge) {v, head[u], ps}, head[u] = cnt;
}

vector<int> ot[maxn], in[maxn];
int vis[maxn], mx(0);
vector<int> ans;

void dfs(int p) {
    vis[p] = 1;
    while(head[p] != 0 && vise[head[p]]) head[p] = edg[head[p]].next;
    if(head[p] == 0) return ;
    vise[head[p]] = vise[head[p] ^ 1] = 1;
    int x = edg[head[p]].ps;
    dfs(edg[head[p]].to);
    ans.emplace_back(x);
//    ans.emplace_back(p);
}

vector<int> out;

int buf[maxn];

signed main() {
	int i, j;
    r1(m);
    for(i = 1; i <= m; ++ i) {
        r1(n);
        mx = max(mx, n);
        for(j = 1; j <= n; ++ j) {
            int x; r1(x);
            a[i].emplace_back(x);
            tmp[++ tot] = x;
        }
    }
    sort(tmp + 1, tmp + tot + 1);
    tot = unique(tmp + 1, tmp + tot + 1) - tmp - 1;
    int ts(0);
    for(i = 1; i <= m; ++ i) {
        for(j = 0; (unsigned) j < a[i].size(); ++ j) {
            int id = lower_bound(tmp + 1, tmp + tot + 1, a[i][j]) - tmp;
            ++ buf[id];
            add(i, m + id, ts), add(id + m, i, ts), ++ ts;
        }
    }
    for(i = 1; i <= tot; ++ i) if(buf[i] & 1) return puts("NO"), 0;
//    printf("tot = %d\n", tot + mx);
    for(i = 1; i <= m + tot; ++ i) if(head[i]) dfs(i);
//    reverse(ans.begin(), ans.end());
    out.resize(ts);
    int op = 1;
//    puts("Test : ");
//    for(int i : ans) {
//        printf("%d ", i);
////        if(i > m) printf("C%d ", i - m);
////        else printf("%d ", i);
//    }
//    puts("\n Ed \n");
    for(int i : ans) out[i] = op, op ^= 1;
    puts("YES");
    char prt[2] = {'L', 'R'};
    op = 0;
    for(i = 1; i <= m; ++ i) {
        for(j = 0; (unsigned) j < a[i].size(); ++ j) printf("%c", prt[out[op ++]]);
        puts("");
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }
```
