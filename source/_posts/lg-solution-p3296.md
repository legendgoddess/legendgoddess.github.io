---
title: "P3296 [SDOI2013]刺客信条 / assassin 题解"
date: 2022-4-14 14:32:00
tags:
  - 二分图
  - 图论
  - Dp
categories:
  - 洛谷题解
---

[P3296 [SDOI2013]刺客信条](https://www.luogu.com.cn/problem/P3296)

**题目简化**：

给定一棵树，每个点有两个 $0, 1$ 权值，合适地安排节点在同构树中的顺序，使得前后对应的权值不同节点个数最小，并输出。

本质上就是对于同构树来考虑每棵子树如何配对的问题，所以先找到中重心之后使用树 $\tt Hash$ 判断出同构。

对于同构的两棵树考虑分成若干组子树，每组子树都是同构的，对于每组子树我们事实上可以任意匹配，需要选择出最优解保证每棵子树都有匹配且边权最小。这个本质就是最小权二分图匹配，直接使用 $\tt KM$ 即可。

复杂度是 $O(n^3)$ 的。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

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

const int maxn = 700 + 5;
int n, m;

namespace SKM {
    typedef long long int64;
    int n, w[maxn][maxn];
    const int64 inf = 1e9;
    bool vis[maxn];
    int mt[maxn], pre[maxn];
    int64 ex[maxn], ey[maxn], sl[maxn];
    void bfs(int u) {
        int x, y(0), yy(0), i; int64 tmp;
        memset(pre, 0, sizeof(pre));
        for(i = 1; i <= n; ++ i) sl[i] = inf;
        mt[y] = u;
        while(1) {
            x = mt[y], tmp = inf, vis[y] = 1;
            for(i = 1; i <= n; ++ i) if(!vis[i]) {
                if(sl[i] > ex[x] + ey[i] - w[x][i]) {
                    sl[i] = ex[x] + ey[i] - w[x][i];
                    pre[i] = y;
                }
                if(sl[i] < tmp) tmp = sl[i], yy = i;
            }
            for(i = 0; i <= n; ++ i) {
                if(vis[i]) ex[mt[i]] -= tmp, ey[i] += tmp;
                else sl[i] -= tmp;
            }
            y = yy;
            if(mt[y] == -1) break;
        }
        while(y) mt[y] = mt[pre[y]], y = pre[y];
    }

    int KM() {
        memset(ex, 0, sizeof(ex));
        memset(ey, 0, sizeof(ey));
        memset(mt, -1, sizeof(mt));
        for(int i = 1; i <= n; ++ i) {
            memset(vis, 0, sizeof(vis));
            bfs(i);
        }
        int64 res(0);
        for(int i = 1; i <= n; ++ i) if(mt[i] != -1)
            res += w[mt[i]][i];
        return - res;
    }
}

int head[maxn], cnt(1);
struct Edge {
    int to, next;
}edg[maxn << 1];
void add(int u,int v) {
    edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
}

int c1[maxn][maxn], c2[maxn][maxn], H[maxn], siz[maxn];
int Tmp(1e9), root;
constexpr int mod = 1e9 + 7, base = 13331;

void fdrt(int p,int pre) {
    siz[p] = 1; int mx(0);
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        fdrt(to, p);
        siz[p] += siz[to];
        mx = max(mx, siz[to]);
    }
    mx = max(mx, n - siz[p]);
    if(mx < Tmp) root = p, Tmp = mx;
}

void dfs(int p,int pre,int sn[][maxn]) {
    H[p] = 5201314046ll % mod;
    memset(sn[p], 0, sizeof(sn[p]));
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        dfs(to, p, sn);
        sn[p][++ sn[p][0]] = to;
    }
    sort(sn[p] + 1, sn[p] + sn[p][0] + 1, [&](const int& x, const int& y) { return H[x] < H[y]; });
    for(int i = 1; i <= sn[p][0]; ++ i) H[p] = ( (1ll * base * H[p]) ^ H[sn[p][i]] ) % mod;
}

int sa[maxn], s[maxn];
int f[maxn][maxn];

int Solve(int x,int y) {// the mincost we need to change tree x to y
    if(f[x][y] != -1) return f[x][y];
    int& res = f[x][y]; res = s[x] ^ sa[y];
    int num = c1[x][0];
    for(int i = 1; i <= num; ++ i) {
        int j = i;
        while(j <= num && H[c1[x][j + 1]] == H[c1[x][i]]) ++ j;
        for(int k = i; k <= j; ++ k) for(int z = i; z <= j; ++ z) Solve(c1[x][k], c2[y][z]), void();
        for(int k = i; k <= j; ++ k) for(int z = i; z <= j; ++ z) {
            int tmp = Solve(c1[x][k], c2[y][z]);
            SKM::w[k - i + 1][z - i + 1] = - tmp;
        }
        SKM::n = j - i + 1;
        res += SKM::KM();
        i = j;
    }
    return res;
}

signed main() {
//    freopen("2.in", "r", stdin);
//    freopen("q2.out", "w", stdout);
	int i, j;
    r1(n);
    for(i = 1; i < n; ++ i) {
        int u, v; r1(u, v), add(u, v), add(v, u);
    }
    for(i = 1; i <= n; ++ i) r1(s[i]);
    for(i = 1; i <= n; ++ i) r1(sa[i]);
    fdrt(1, 0);
    dfs(root, 0, c2);
    int tmp = H[root], ans(1e9);
    for(i = 1; i <= n; ++ i) {
        dfs(i, 0, c1);
        if(H[i] == tmp) {
            memset(f, -1, sizeof(f));
            ans = min(ans, Solve(i, root));
//            printf("i = %d\n", i);
        }
    }
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```

