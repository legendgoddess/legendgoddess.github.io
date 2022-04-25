---
title: "CF700E Cool Slogans 题解"
date：2022-4-24 19:34:00
tags:
  - 后缀自动机
categories:
  - CF题解
---

####  [CF700E Cool Slogans](https://www.luogu.com.cn/problem/CF700E)

首先子串变成 $\tt SAM$，我们考虑出现两次不妨考虑在区间 $[l, r]$ 那么我们的串肯定也是 $[l, r]$ 多了肯定是不优的。

然后对于 $s_i, s_{i - 1}$ 可以发现 $s_{i - 1}$ 是顶着 $s_i$ 的左右侧的，我们可以随便钦定一个进行考虑，不妨考虑 $s_{i - 1}$ 是 $s_{i}$ 的后缀。

考虑后缀自动机上 $x, y$ 其中 $y$ 是 $x$ 的祖先。如何描述 $x$ 中出现了两次 $y$。

我们明确 $\tt endpos$ 集合在 $\tt parent$ 树上就是子树，对于一条链根据定义有上边串是下面串的后缀，所以肯定出现一次的。

我们只需要考虑另外一个点的情况，不妨考虑 $x$ 长度为 $len(x)$，可以发现如果 $y$ 的另外结束节点是在 $[pos(x) - len(x) + len(y), pos(x) - 1]$ 那么肯定是合法的。

我们可以使用经典套路线段树合并来查询。

考虑如何计算答案，如果上述 $x, y$ 是合法的我们考虑设 $f(x)$ 表示以 $x$ 结尾的答案。

但是问题来了，我们记录的都是最长串这个能代表当前集合吗？

先明确对于一个 $y$ 其使用最长串的限制是最严格的，因为 $\tt endpos$ 集合是相同的，我们贪心接纳尽可能多的子串所以选择最长的 $y$。

同样对于 $x$ 因为 $\tt endpos$ 集合是相同的，所以我们选择哪个点对于之后的匹配都是没有影响的，所以选择可能性最大的。

之后考虑进行 $\tt Dp$，设 $f(i)$ 表示点 $i$ 的答案，我们每次肯定尽量匹配短的，所以能匹配就匹配。

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

const int maxn = 4e5 + 5;
const int maxm = 1e7 + 5;
int n, m;
int rt[maxn], itot(0), t[maxm], lson[maxm], rson[maxm];
#define ls lson[p]
#define rs rson[p]
#define mid ((l + r) >> 1)

int Insert(int pr,int l,int r,int pos) {
    int p = ++ itot;
    if(l == r) return p;
    if(pos <= mid) ls = Insert(lson[pr], l, mid, pos);
    else rs = Insert(rson[pr], mid + 1, r, pos);
    return p;
}

int merge(int u,int v) {
    if(!u || !v) return u | v;
    int op = ++ itot;
    lson[op] = merge(lson[u], lson[v]);
    rson[op] = merge(rson[u], rson[v]);
    return op;
}

int Ask(int p,int l,int r,int ll,int rr) {
    if(!p) return 0;
    if(ll <= l && r <= rr) return 1;
    int res(0);
    if(ll <= mid) res |= Ask(ls, l, mid, ll, rr);
    if(mid < rr) res |= Ask(rs, mid + 1, r, ll, rr);
    return res;
}

#undef ls
#undef rs
#undef mid
char s[maxn];
struct Node {
    int ch[26], fa, ln;
}d[maxn];
int pos[maxn];
int las(1), tot(1);
void Insert(int c,int id) {
    int p = las, np = las = ++ tot;
    pos[np] = id, d[np].ln = d[p].ln + 1;
    for(; p && !d[p].ch[c]; p = d[p].fa) d[p].ch[c] = np;
    if(!p) d[np].fa = 1;
    else {
        int q = d[p].ch[c];
        if(d[q].ln == d[p].ln + 1) d[np].fa = q;
        else {
            int nq = ++ tot; d[nq] = d[q], pos[nq] = pos[q];
            d[nq].ln = d[p].ln + 1;
            d[q].fa = d[np].fa = nq;
            for(; p && d[p].ch[c] == q; p = d[p].fa) d[p].ch[c] = nq;
        }
    }
}

int buc[maxn], b[maxn];
int f[maxn], g[maxn];

signed main() {
	int i, j;
    r1(n);
    scanf("%s", s + 1);
    for(i = 1; i <= n; ++ i) Insert(s[i] - 'a', i);
    int p = 1;
    for(i = 1; i <= n; ++ i) {
        p = d[p].ch[s[i] - 'a'];
        rt[p] = Insert(0, 1, n, i);
    }
    for(i = 1; i <= tot; ++ i) ++ buc[d[i].ln];
    for(i = 1; i <= n; ++ i) buc[i] += buc[i - 1];
    for(i = 1; i <= tot; ++ i) b[buc[d[i].ln] --] = i;
//    for(i = 1; i <= tot; ++ i) printf("%d : %d\n", i, pos[b[i]]);
    for(i = tot; i > 1; -- i) rt[d[b[i]].fa] = merge(rt[d[b[i]].fa], rt[b[i]]);
    int ans(1);
    for(i = 1; i <= tot; ++ i) {
        int x = b[i];
        if(d[x].fa == 1) { f[x] = 1, g[x] = x; continue; }
//        printf("(%d, %d)\n", pos[x] - d[x].ln + d[d[x].fa].ln, pos[x] - 1);
        if(Ask(rt[g[d[x].fa]], 1, n, pos[x] - d[x].ln + d[g[d[x].fa]].ln, pos[x] - 1))
            f[x] = f[d[x].fa] + 1, g[x] = x;
        else f[x] = f[d[x].fa], g[x] = g[d[x].fa];
        ans = max(ans, f[x]);
    }
    printf("%d\n", ans);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```

