---
title: "CF917E Upside Down 题解"
date: 2022-4-26 15:11:00
tags:
  - 字符串
  - 后缀数组
  - AC自动机
  - 数论
categories:
  - CF题解
---

####  [CF917E Upside Down](https://www.luogu.com.cn/problem/CF917E)

> 比较有趣的一个题。

对于 $i \to j$ 的路径我们很容易想到分成两部分进行计算：

- $i \to lca$
- $lca \to j$
- 跨过 $lca$

对于前面的部分我们建立 $\tt ac$ 自动机，之后本质上就是单点加区间查询，我们树状数组维护一下。

我们方便处理的是从 $lca \to i$ 的路径，那么我们考虑对于不方便的情况直接建立反串的 $\tt ac$ 自动机即可。

对于跨过的部分我们不妨考虑字符串的分界点为 $x$，那么左边就是 $[1, x]$ 右边是 $[x + 1, n]$。对于两边发现有一个端点是固定的，我们考虑根据这个进行计算。

我们先考虑找到一个合法的串，我们不妨只考虑 $[x + 1, n]$ 的情况另一种情况同理，贪心考虑可以知道这个串右端点是固定的，考虑两段从 $\tt lca$ 经过若干步走到字符串末尾的串设长度为 $a, b, a < b$。因为末尾部分是相同的，所以 $a$ 是 $b$ 的 $\tt border$，那么我们贪心可以考虑选取尽可能长的串。

选取串我们可以考虑尽可能长的串，经过简单转换可以变成 $\tt lcp$ 尽量大的串，看到 $\tt lcp$ 会考虑使用后缀数组。具体来说我们二分 $\tt rank$ 之后求出最长的 $\tt lcp$ 这个我们可以通过倍增 $\tt hash$ 来解决。

对于两个串的匹配，假设两个串长度分别为 $a, b$ 字符串总长为 $n$。我们有 $a + b = n$，根据字符串 $\tt border$ 的性质我们可以划分成 $O(\log n)$ 的等差数列，对于等差数列内部我们暴力进行匹配，本质上就是找 $ax+ by = c$ 的自然数解，可以使用 $\tt exgcd$ 解决。

对于询问直接离线到点上即可。

**注意：** 考虑 $lca \to u$ 的路径，我们需要保证不存在一个串穿过 $\tt lca$ 的情况被计算，所以减去贡献的点需要相对向下平移一下。

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
int n, m, Q, head[maxn], cnt(1);
struct Edge {
    int to, next, w;
}edg[maxn << 1];
void add(int u,int v,int w) {
    edg[++ cnt] = (Edge) {v, head[u], w}, head[u] = cnt;
}
char _s[maxn];
constexpr int mod1 = 1e9 + 7, mod2 = 1e9 + 9;
struct Hash {
    int a, b;
    Hash(int x = 0,int y = 0) : a(x), b(y) {}
    Hash operator + (const Hash& z) { return (Hash){(a + z.a) % mod1, (b + z.b) % mod2}; }
    Hash operator - (const Hash& z) { return (Hash){(a - z.a + mod1) % mod1, (b - z.b + mod2) % mod2}; }
    Hash operator * (const Hash& z) { return (Hash){1ll * a * z.a % mod1, 1ll * b * z.b % mod2}; }
    bool operator == (const Hash& z) { return (a == z.a) && (b == z.b); }
}pw[maxn], th[maxn];
const Hash base = (Hash){13331, 131};
int fa[maxn][21], cl[maxn], dep[maxn];
Hash Thas(int u,int lc) {
    return th[u] - th[lc] * pw[dep[u] - dep[lc]];
}

void dfs(int p,int pre) {
    fa[p][0] = pre, dep[p] = dep[pre] + 1;
    for(int i = 1; i < 19; ++ i) fa[p][i] = fa[fa[p][i - 1]][i - 1];
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        cl[to] = edg[i].w, th[to] = th[p] * base + (Hash){cl[to], cl[to]};
        dfs(to, p);
    }
}

int Lca(int u,int v) {
    if(dep[u] < dep[v]) swap(u, v);
    int d = dep[u] - dep[v];
    for(int i = 18; i >= 0; -- i) if((d >> i) & 1) u = fa[u][i];
    if(u == v) return u;
    for(int i = 18; i >= 0; -- i) {
        if(fa[u][i] != fa[v][i]) {
            u = fa[u][i], v = fa[v][i];
        }
    }
    return fa[u][0];
}

int Jump(int u,int k) {
    for(int i = 18; i >= 0; -- i) if((k >> i) & 1) u = fa[u][i];
    return u;
}

struct ACAM {
    int tr[maxn][27], fl[maxn], tot, pos[maxn];
    vector<int> vc[maxn];
    ACAM(void) : tot(1) {}
    void Insert(char* str,int m,int id) {
        int p = 1;
        for(int i = 1; i <= m; ++ i) {
            int x = str[i] - 'a' + 1;
            if(!tr[p][x]) tr[p][x] = ++ tot;
            p = tr[p][x];
        }
        pos[id] = p;
    }
    int dfn[maxn], edfn[maxn], dfntot;
    void dfs(int p) {
        dfn[p] = ++ dfntot;
        for(const int& v : vc[p]) dfs(v);
        edfn[p] = dfntot;
    }
    void build() {
        static queue<int> q; while(!q.empty()) q.pop();
        for(int i = 1; i <= 26; ++ i) if(tr[1][i]) q.push(tr[1][i]), fl[tr[1][i]] = 1;
        else tr[1][i] = 1;
        while(!q.empty()) {
            int u = q.front(); q.pop();
            for(int v, i = 1; i <= 26; ++ i) {
                v = tr[u][i];
                if(v) {
                    q.push(v), fl[v] = tr[fl[u]][i];
                }
                else tr[u][i] = tr[fl[u]][i];
            }
        }
        for(int i = 2; i <= tot; ++ i) vc[fl[i]].emplace_back(i);
        dfntot = 0;
        dfs(1);
    }
    int t[maxn];
    int lowbit(int x) { return x & -x; }
    void add(int p,int c) { assert(p != 0); p = dfn[p]; for(; p <= tot; p += lowbit(p)) t[p] += c;}
    int ask(int p) { int res(0); for(; p > 0; p -= lowbit(p)) res += t[p]; return res; }
    int query(int x) { x = pos[x]; return ask(edfn[x]) - ask(dfn[x] - 1); }
}Ta[2];
// Arithmetic sequence
struct Arith {
    int s, t, d;
};

struct KMP {
    int *bel, *nxt, *del, n;
    void Insert(char* str,int m) {
        bel = new int[m + 2] ();
        nxt = new int[m + 2] ();
        del = new int[m + 2] ();
        n = m;
        del[1] = 1, bel[1] = 1;
        for(int i = 2, j = 0; i <= n; ++ i) {
            while(j && str[j + 1] != str[i]) j = nxt[j];
//            printf("j = %d\n", j);
            if(str[j + 1] == str[i]) ++ j;
            del[i] = i - j, nxt[i] = j;
            bel[i] = (del[i] == del[j] ? bel[j] : i);
        }
    }
    void Get(int p,int len,Arith* ret, int& tot) const {
        for(; p > len; p = nxt[p]) ;
        while(p > 0) {
            int s = bel[p], t = p, d = del[p];
            ret[++ tot] = {s, t, d};
            p = nxt[s];
        }
    }
}p[2][maxn];

int buc[maxn];

struct SA {
    int *a, *sa, *sa2, *rk, n, m;
    Hash *h;

    void Sort() {
        for(int i = 1; i <= m; ++ i) buc[i] = 0;
        for(int i = 1; i <= n; ++ i) buc[rk[i]] ++;
        for(int i = 1; i <= m; ++ i) buc[i] += buc[i - 1];
        for(int i = n; i >= 1; -- i) sa[buc[rk[sa2[i]]] --] = sa2[i];
    }

    void Insert(char *str, int ln) {
        n = ln, m = 26;
        a = new int[n + 2] ();
        sa = new int[n + 2] ();
        sa2 = new int[n + 2] ();
        rk = new int[n + 2] ();
        h = new Hash[n + 2] ();
        for(int i = 1; i <= n; ++ i) a[i] = str[i] - 'a' + 1, h[i] = h[i - 1] * base + (Hash){a[i], a[i]};
        for(int i = 1; i <= n; ++ i) rk[i] = a[i], sa2[i] = i;
        Sort();

        for(int i = 1; i <= n; i <<= 1) {
            int num(0);
            for(int j = n - i + 1; j <= n; ++ j) sa2[++ num] = j;
            for(int j = 1; j <= n; ++ j) if(sa[j] > i)
                sa2[++ num] = sa[j] - i;
            Sort();
            swap(rk, sa2);
            num = 1;
            rk[sa[1]] = 1;
            for(int j = 2; j <= n; ++ j)
                rk[sa[j]] = (sa2[sa[j]] == sa2[sa[j - 1]] && sa2[sa[j] + i] == sa2[sa[j - 1] + i]) ? num : ++ num;
            if(num >= n) break;
            m = num;
        }
    }

    Hash Has(int l,int r) {
        return h[r] - h[l - 1] * pw[r - l + 1];
    }

    int lcp(int id, int u,int lc) {
        int x = sa[id], ln = n - x + 1;
        if(u == lc) return 0;
        if(ln >= dep[u] - dep[lc] && Thas(u, lc) == Has(x, x + dep[u] - dep[lc] - 1)) return dep[u] - dep[lc];
        for(int i = 18; i >= 0; -- i) if(dep[u] - dep[lc] > (1 << i))
            if( (ln < dep[fa[u][i]] - dep[lc]) || ( !(Thas(fa[u][i], lc) == Has(x, x + dep[fa[u][i]] - dep[lc] - 1)) ) ) {
                u = fa[u][i];
            }
        u = fa[u][0];
        return dep[u] - dep[lc];
    }

    bool check(int mid, int u,int lc) {
        int L = lcp(mid, u, lc);
        if(L == n - sa[mid] + 1) return 1;
        if(L == dep[u] - dep[lc]) return 0;
        return a[sa[mid] + L] < cl[Jump(u, dep[u] - dep[lc] - L - 1)];
    }

    void Solve(int u,int lc,KMP& kp, Arith* ret, int& num) {
        int l = 1, r = n, mid, ans(0);
        while(l <= r) {
            mid = (l + r) >> 1;
            if(check(mid, u, lc)) ans = mid, l = mid + 1;
            else r = mid - 1;
        }
        num = 0;
        if(!ans) return ;
        int p = n - sa[ans] + 1, ln = lcp(ans, u, lc);
        kp.Get(p, ln, ret, num);
    }

}s[2][maxn];

long long ans[maxn];

struct Quer {
    int id, opt, C, str;
};
vector<Quer> q[maxn];
int sstr[maxn];

void dfs2(int p,int pre,int num0, int num1) {
//    assert(num0 != 0 && num1 != 0);
    Ta[0].add(num0, 1), Ta[1].add(num1, 1);
    for(const Quer& v : q[p]) {
        ans[v.id] += v.C * Ta[v.opt].query(v.str);
    }
    for(int i = head[p];i;i = edg[i].next) {
        int to = edg[i].to; if(to == pre) continue;
        int c = edg[i].w;
        dfs2(to, p, Ta[0].tr[num0][c], Ta[1].tr[num1][c]);
    }
    Ta[0].add(num0, - 1), Ta[1].add(num1, - 1);
}

void qadd(int u,int lc,int c,int id,int op) {
    if(dep[u] - dep[lc] < sstr[c]) return ;
    q[u].push_back({id, op, 1, c});
    int x = Jump(u, dep[u] - dep[lc] - sstr[c] + 1);
    q[x].push_back({id, op, - 1, c});
}

namespace Chain {
    Arith a1[65], a2[65];
    int n1, n2;
    int exgcd(int a,int b,int& x,int& y) {
        if(b == 0) return x = 1, y = 0, a;
        int gc = exgcd(b, a % b, x, y); long long z = x;
        x = y, y = z - a / b * y;
        return gc;
    }

    int Calc(int a,int b,int ua,int ub,int c) {
        int x, y, x1, y1, dx, dy, mx;
        int gd = exgcd(a, b, x, y);
        if(c % gd != 0) return 0;
        x1 = c / gd * x, y1 = c / gd * y;
        dx = b / gd, dy = a / gd, mx = x1 % dx;
        if(mx < 0) mx += dx;
        y1 += ((x1 - mx) / dx) * dy, x1 = mx;
        if(y1 < 0) return 0;
        if(y1 > ub) {
            int tmp = (y1 - ub - 1) / dy + 1;
            y1 -= tmp * dy, x1 += tmp * dx;
        }
        if(x1 < 0 || y1 < 0 || x1 > ua || y1 > ub) return 0;
        return min((ua - x1) / dx + 1, y1 / dy + 1);
    }

    int calc(Arith& x, Arith& y,int ln) {
        return Calc(x.d, y.d, (x.t - x.s) / x.d, (y.t - y.s) / y.d, ln - x.s - y.s);
    }

    void Solve(int u, int v,int lc, int id,int qi) {
        s[1][id].Solve(u, lc, p[0][id], a1, n1);
        s[0][id].Solve(v, lc, p[1][id], a2, n2);
//        assert(n1 < 60 && n2 < 60);
        int ln = sstr[id];
        for(int a = 1; a <= n1; ++ a) for(int b = 1; b <= n2; ++ b)
        if(a1[a].s + a2[b].s <= ln && a1[a].t + a2[b].t >= ln)
            ans[qi] += calc(a1[a], a2[b], ln);
    }
}

signed main() {
	int i, j;
    r1(n, m, Q);
    pw[0] = {1, 1};
    for(i = 1; i < maxn; ++ i) pw[i] = pw[i - 1] * base;
    for(i = 1; i < n; ++ i) {
        int u, v; r1(u, v), scanf("%s", _s + 1);
        add(u, v, _s[1] - 'a' + 1), add(v, u, _s[1] - 'a' + 1);
    }
    dfs(1, 0);
    for(i = 1; i <= m; ++ i) {
        scanf("%s", _s + 1);
        int ln = strlen(_s + 1);
        sstr[i] = ln;
        Ta[0].Insert(_s, ln, i);
        p[0][i].Insert(_s, ln);
        s[0][i].Insert(_s, ln);
        reverse(_s + 1, _s + ln + 1);
        Ta[1].Insert(_s, ln, i);
        p[1][i].Insert(_s, ln);
        s[1][i].Insert(_s, ln);
    }
    Ta[0].build(), Ta[1].build();
    /*
struct Quer {
    int id, opt, C, str;
};
    */
    for(i = 1; i <= Q; ++ i) {
        int u, v, c;
        r1(u, v, c);
        int lc = Lca(u, v);
        qadd(u, lc, c, i, 1), qadd(v, lc, c, i, 0);
        Chain::Solve(u, v, lc, c, i);
    }
//    for(i = 1; i <= Q; ++ i) printf("%d : %lld\n", i, ans[i]);
    dfs2(1, 0, 1, 1);
    for(i = 1; i <= Q; ++ i) printf("%lld\n", ans[i]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//
```

