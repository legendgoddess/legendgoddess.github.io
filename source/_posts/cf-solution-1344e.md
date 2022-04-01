---
title: "CF1344E Train Tracks 题解"
date: 2022-4-1 8:45:00
tags:
  - LCT
categories:
  - CF题解
---

[Problem - 1344E - Codeforces](https://codeforces.com/problemset/problem/1344/E)

考虑我们肯定是对于每个点最终肯定是要进行若干次操作，每次操作是有时间限制的，不妨考虑需要在区间 $[l_i, r_i]$ 之间做完，那么对于这个东西我们肯定是考虑扫描线 $l$ 对于最小的 $r$ 进行贪心处理。

那么如果处理出来这个区间呢？本质上就是两条边进行更新的时候对于虚边进行翻转，本质就是 $\tt LCT$ 的 $\tt access$ 操作，那么时空复杂度就是和 $\tt LCT$ 操作类似。

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
#define int long long
const int maxn = 2e5 + 5;
int n, m;
int head[maxn], cnt(1);
struct Edge {
    int to, next, w;
}edg[maxn << 1];
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}
int s[maxn], t[maxn];

int fa[maxn], ch[maxn][2], v[maxn], tg[maxn], d[maxn];

void dfs1(int p,int pre) {
    fa[p] = pre;
    v[p] = - d[p];
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        if(!ch[p][1]) ch[p][1] = to;
        d[to] = d[p] + edg[i].w;
        dfs1(to, p);
    }
}

int nrt(int p) { return p == ch[fa[p]][1] || p == ch[fa[p]][0]; }
int pd(int p) { return p == ch[fa[p]][1]; }

void Add(int p,int c) {
    if(!p) return ;
    v[p] = tg[p] = c;
}

void pushdown(int p) {
    if(!tg[p]) return ;
    Add(ch[p][0], tg[p]), Add(ch[p][1], tg[p]);
    tg[p] = 0;
}

void Rotate(int p) {
    int y = fa[p], yy = fa[y], k = pd(p);
    if(nrt(y)) ch[yy][pd(y)] = p;
    fa[p] = yy, fa[y] = p;
    fa[ch[p][!k]] = y, ch[y][k] = ch[p][!k];
    ch[p][!k] = y;
}

void Splay(int p) {
    static int st[maxn]; int ed(0), x = p;
    while(nrt(x)) st[++ ed] = x, x = fa[x];
    while(ed > 0) pushdown(st[ed --]);
    for(int f; f = fa[p], nrt(p); Rotate(p)) if(nrt(fa[p])) {
        Rotate(pd(p) == pd(f) ? f : p);
    }
}

int l[maxn * 20], r[maxn * 20], ct(0);

void access(int p,int t) {
    int u = p;
    for(int y = 0; p; y = p, p = fa[p]) {
        Splay(p);
        if(y) {
            ch[p][1] = y;
            l[++ ct] = v[p] + d[p] + 1;
            r[ct] = t + d[p];
        }
    }
    Splay(u);
    Add(ch[u][0], t);
}

priority_queue<pair<int, int>> q;

int pos(0);
int rk[maxn * 20];

void spread(int dy) {
    while(pos < ct && l[rk[pos + 1]] <= dy) {
        q.push({- r[rk[pos + 1]], rk[pos + 1]});
        ++ pos;
    }
}

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i < n; ++ i) {
        int u, v, w;
        r1(u, v, w); add(u, v, w), add(v, u, w);
    }
    for(i = 1; i <= m; ++ i) r1(s[i], t[i]);
    dfs1(1, 0), ct = 0;
    for(i = 1; i <= m; ++ i) access(s[i], t[i]);
    for(i = 1; i <= ct; ++ i) rk[i] = i;
    sort(rk + 1, rk + ct + 1, [&](const int& a, const int& b){
        return l[a] < l[b];
    });
//    for(i = 1; i <= ct; ++ i) printf("%d : [%d %d]\n", i, l[i], r[i]);
    int dy(1), last(0);
    spread(1);
    map<int, int> times;
    while(1) {
        while(!q.empty()) {
            int u = q.top().second; q.pop();
            if(r[u] > last) last = r[u];
            if(dy > r[u]) {
                int ans(0);
                for(auto v : times) if(v.first < r[u]) ans += v.second;
                printf("%lld %lld\n", r[u], ans);
                return 0;
            }
            ++ dy, spread(dy);
            times[r[u]] ++;
        }
        if(pos == ct) break;
        dy = max(dy, l[rk[pos + 1]]);
        spread(dy);
    }
    printf("-1 %lld\n", ct);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
