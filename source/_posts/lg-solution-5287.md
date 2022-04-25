---
title: "P5287 [HNOI2019]JOJO 题解"
date: 2022-4-25 8:20:00
tags:
  - 字符串
  - border
categories:
  - 洛谷题解
---

#### [P5287 [HNOI2019]JOJO](https://www.luogu.com.cn/problem/P5287)

- 加入 $x$ 个字符 $c$，保证相邻加入操作 $c$ 不同。
- 查询每个前缀的 $\tt border$ 长度和。

如果不看输入感觉没有什么优秀的做法，输入提示了 $x$ 个 $c$ 肯定和做法有关。

我们尝试计算加入 $c$ 的 $\tt border$，首先找到加入前串 $S$ 的 $\tt border$，之后如果后面恰好有 $\ge x$ 个 $c$ 说明是可以匹配的，如果没有说明这个不是 $\tt border$，考虑继续向前找当前 $\tt border$ 的 $\tt border$。

既然相邻操作 $c$ 是不同的，考虑将一次操作看成一个点，$S$ 的 $\tt border$ 的结尾肯定恰好是一个完整点，不然之后不可能跟上 $c$。考虑跳 $\tt border$ 后每次后面跟着的 $c$ 是逐渐变多的，考虑如果减少的话 $\tt border$ 是可以增长的。

然后对于一段都能匹配是一个等差数列求和。

我们考虑暴力跳 $\tt border$ 是不行的，考虑优化，我们如果 $\tt border$ 的长度 $\ge \frac{|S|}{2}$ 那么就会产生循环节，我们考虑对于相同的循环节我们只需要计算一个即可，对于 $< \frac{|S|}{2}$ 的情况，我们暴力跳，这样我们跳 $\tt border$ 的次数就是 $O(\log n)$ 级别的。

> 对于前一种情况，还有一种说法就是将串 $S$ 进行 $\tt border$ 的等差数列划分，这样的划分是 $O(\log |S|)$ 级别的。
>
> 将 $\tt SAM$ 上每个节点的 $\tt endpos$集合划分为若干等差序列，等差序列的总个数是 $O(n \sqrt n)$ 的。
>
> - 对于 $len < \sqrt n$ 的集合，因为相同长度的 $len$ 集合是不交的，所以总个数是 $O(n \sqrt n)$。
> - 对于 $len > \sqrt n$ 的集合， 若有两个**相邻**的 $\tt edp$ 距离不超过 $\frac{len}{2}$ ，则距离必然为最小循环节。由此，只有 $> \frac{len}{2}$ 的空位才能隔断某个等差数列，故每个点的等差数列个数至多是 $O(\frac{n}{len}) = O(\sqrt n)$ 的。
>
> > 这个最小循环节可以通过 $\tt border$ 理论得出。

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
//#define int long long
const int maxn = 2e5 + 5;
constexpr int mod = 998244353;
typedef pair<int, int> pr;
int n, m, ans[maxn], ln[maxn], fl[maxn], fl1[maxn], ed(0), prs[maxn], go[maxn];
vector<int> vc[maxn];
char s[10];
pr a[maxn];
bool vis[maxn];
int clc(int l,int r) { return (1ll * (l + r) * (r - l + 1) >> 1) % mod; }
pair<int*, int> st[maxn * 6];
pr b[maxn];

void Ins(int &a,int b) { st[++ ed] = {&a, a}, a = b; }
void Roll(int x) {
    while(ed > x) {
        *(st[ed].first) = st[ed].second;
        -- ed;
    }
}
void Add(int &a,long long b) { a = (a + b) % mod; }

void Insert(int p,int c,int x) {
//    if(p == 6) puts("yes");
    int l = ++ ln[p];
    Ins(b[l].first, c), Ins(b[l].second, x), Ins(prs[l], prs[l - 1] + x);
    if(l == 1) return Add(ans[p], clc(0, x - 1));
    int np = fl[l - 1], lim = 1;
//    if(p == 6) {
//        for(int i = 1; i <= l; ++ i) printf("(%c, %d) = %d\n", 'a' + b[i].first - 1, b[i].second, fl[i]);
//    }
    while(np >= 0 && b[np + 1] != b[l]) {
        if(b[np + 1].first == b[l].first && b[np + 1].second >= lim && lim <= x) {
            Add(ans[p], clc(prs[np] + lim, prs[np] + min(x, b[np + 1].second)));
            lim = min(b[np + 1].second, x) + 1;
//            if(p == 7) printf("S : %d \n", ans[p]);
        }
        if(go[np + 1]) np = fl1[np + 1] - 1;
        else np = fl[np];
    } ++ np;
    if(np && (l - np) * 2 <= l) Ins(go[l], 1), Ins(fl1[l], fl[l % (l - np) + l - np]);
    if(!np && b[1].first == c && b[1].second <= x) ++ np;
    if(np) {
        if(b[np].second >= lim && lim <= x) {
            Add(ans[p], clc(prs[np - 1] + lim, prs[np - 1] + min(b[np].second, x)));
            lim = min(b[np].second, x) + 1;
        }
        if(np == 1 && lim <= x) {
            Add(ans[p], 1ll * prs[np] * (x - lim + 1));
            lim = x + 1;
        }
    }
//    if(p == 6) printf("np = %d\n", np);
    Ins(fl[l], np);
}

void dfs(int p) {
    int tmp = ed;
    if(vis[p]) Insert(p, a[p].first, a[p].second);
    for(int v : vc[p]) {
        ln[v] = ln[p], ans[v] = ans[p];
        dfs(v);
    }
    Roll(tmp);
}

signed main() {
	int i, j;
    r1(n);
    fl[0] = -1;
    for(i = 1; i <= n; ++ i) {
        int opt, x;
        r1(opt, x);
        if(opt == 2) { vc[x].emplace_back(i); continue; }
        scanf("%s", s + 1);
        a[i] = { s[1] - 'a' + 1, x }, vis[i] = 1;
        vc[i - 1].emplace_back(i);
    }
    dfs(0);
    for(i = 1; i <= n; ++ i) printf("%d\n", ans[i]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```

