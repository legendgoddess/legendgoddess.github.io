---
title: "USACO 2022 US Open Contest Ag 题解"
date: 2022-4-8 8:58:00
tags:
  - 贪心
categories:
  - USACO题解
---

[USACO - Ag - T1](http://usaco.org/index.php?page=viewproblem2&cpid=1230)

内向基环树森林，既然可以自己给定排列我们对于链的答案肯定是全部都能算上的。

对于环发现只会有 $1$ 个点没有算上贡献取最小的即可。

> 或者使用最大生成树也可以，因为本质上一个基环树断掉一条环上的边即可。

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
#define int long long
const int maxn = 2e5 + 5;
int n, m, head[maxn], cnt;
struct Edge {
    int to, next, w;
}edg[maxn << 1];
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}
int vis[maxn], Siz;
vector<int> lp[maxn];
int fa[maxn], d[maxn], fla(0), ans;
void dfs(int p) {
    vis[p] = Siz;
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to;
        if(vis[to] && vis[to] != vis[p]) continue;
        if(vis[to] == vis[p] && !fla) {
            fla = 1;
            int mn(edg[i].w), y = p;
            while(y != to) {
                mn = min(mn, d[y]), y = fa[y];
            }
            ans -= mn;
        }
        else if(!vis[to]) fa[to] = p, d[to] = edg[i].w, dfs(to);

    }
}

signed main() {
	int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) {
        int u, w; r1(u, w);
        add(i, u, w);
        ans += w;
    }
    for(i = 1; i <= n; ++ i) if(!vis[i]) {
        fla = 0;
        ++ Siz;
        dfs(i);
    }
    printf("%lld\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

[USACO - Ag - T2](http://usaco.org/index.php?page=viewproblem2&cpid=1231)

考虑只要相对顺序合法即可。

不妨预处理对于每个 $(a, b)$ 点对记录每个 $a$ 前面 $b$ 出现的个数，之后通过 $\tt hash$ 存储。

复杂度是 $O(26n)$ 的。

查询的时候考虑枚举每个字符的相对位置如果都合法就行。

复杂度是 $O(26^2 n)$ 的。

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

const int maxn = 2e5 + 5;
typedef unsigned int uint32;
const int mod1 = 1e9 + 7;
const int base = 13131;
int n, m;

struct Node {
    char s[maxn];
    vector<int> vc[26];
    uint32 bc[26][26];
    int bc1[26][26];
    int n;
    void init() {
        int i, j;
        scanf("%s", s + 1);
        n = strlen(s + 1);
        for(i = 1; i <= n; ++ i) vc[s[i] - 'a'].push_back(i);
        for(i = 0; i < 18; ++ i) for(j = i + 1; j < 18; ++ j) {
            for(int k = 1; k <= n; ++ k) if(s[k] == (i + 'a') || s[k] == (j + 'a')) {
                bc[i][j] = bc[i][j] * base + s[k];
            }
        }
    }
}S, T;

char c[maxn];
int a[maxn];

signed main() {
//    freopen("2.in", "r", stdin);
//    freopen("2.ans", "w", stdout);
	int i, j;
    S.init(), T.init();
    int Q; r1(Q);
    string ans;
    while(Q --) {
        scanf("%s", c + 1);
        int ts = strlen(c + 1);
        for(i = 1; i <= ts; ++ i) a[i] = c[i] - 'a';
        int lf(1);
        sort(a + 1, a + ts + 1);
        for(i = 1; i <= ts && lf; ++ i) if(S.vc[a[i]].size() != T.vc[a[i]].size()) lf = 0;
        for(i = 1; i <= ts && lf; ++ i) for(j = i + 1; j <= ts; ++ j) {
            if(S.bc[a[i]][a[j]] != T.bc[a[i]][a[j]]) { lf = 0; break; }
        }
        if(lf) ans += 'Y';
        else ans += 'N';
    }
    puts(ans.c_str());
	return 0;
}
/*
aabcd
caabd
1
abd
*/
}


signed main() { return Legendgod::main(), 0; }//


```

[USACO - Ag - T3](http://usaco.org/index.php?page=viewproblem2&cpid=1232)

考虑 $oc$ 可以通过变换 $o \to wc$ 变成 $w$ 然后变成 $co$，所以是有交换律的。

所以考虑最终答案只需要判断区间每种元素的个数。

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

const int maxn = 2e5 + 5;
int n, m;
char s[maxn];
int a[3][maxn];
map<char, int> mp;

signed main() {
	int i, j;
    mp['C'] = 0, mp['O'] = 1, mp['W'] = 2;
    scanf("%s", s + 1);
    n = strlen(s + 1);
    for(i = 1; i <= n; ++ i) {
        a[0][i] += a[0][i - 1];
        a[1][i] += a[1][i - 1];
        a[2][i] += a[2][i - 1];
        a[mp[s[i]]][i] ++;
    }
    int Q; r1(Q);
    string ans = "";
    while(Q --) {
        int l, r; r1(l, r);
        int tmp[3];
        tmp[0] = a[0][r] - a[0][l - 1];
        tmp[1] = a[1][r] - a[1][l - 1];
        tmp[2] = a[2][r] - a[2][l - 1];
        tmp[0] &= 1;
        tmp[1] &= 1;
        tmp[2] &= 1;
//        printf("%d %d %d\n", tmp[0], tmp[1], tmp[2]);
        if(!tmp[0] && tmp[1] && tmp[2]) { ans += 'Y'; continue; }
        if(tmp[0] && !tmp[1] && !tmp[2]) { ans += 'Y'; continue; }
        ans += 'N';
    }
    puts(ans.c_str());
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
