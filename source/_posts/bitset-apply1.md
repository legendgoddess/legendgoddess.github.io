---
title: "bitset 关于字符串匹配的应用"
date: 2022-3-31 19:26:00
tags:
  - STL
categories:
  - 算法
---

事情的起因来自这个：

前情提要：[bitset 和 __builtin 函数 | Legendgod's Blog](https://legendgod.ml/2022/03/28/bitset_function/)

$\text{Ha宝: bitset 还可以加速字符串匹配}$。

$\text{legendgod: /se/se}$。

---

如果手动实现一个 $\tt bitset$ 会怎么搞，肯定是通过一个 $\tt int$ 压起来，之后如果范围内的就查表，如果范围外就直接暴力跳，这样复杂度是有一个 $\frac{1}{w}$ 的常数，同理其一位只需要一个 $\tt Byte$。

---

关于字符串匹配我们考虑对于文本串 $S$，找出每个字符 $c$ 出现的所有位置，存在 $bt_c$ 中。

对于一个串 $T$ 考虑对于其每个位置都与 $S$ 进行一次暴力匹配，不妨设其长度为 $m$。

考虑记录一个答案 $\tt bitset$，$ans$。其中 $ans_i$ 表示位置 $i$ 能否成为答案。

对于位置 $i$ 的元素 $T_i$ 我们考虑让 `ans &= bt[T[i]] << (m - i - 1)`，可以发现这个东西本质就是对于每一位都进行一次验证。

> 串是从 $0$ 开始的。

最终 $ans$ 在合法区间内的 $1$ 就是答案了。

---

[Substrings in a String - 洛谷](https://www.luogu.com.cn/problem/CF914F)

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

const int maxn = 1e5 + 5;
int n, m;
bitset<maxn> bt[26];
bitset<maxn> ans;
char S[maxn], t[maxn];

signed main() {
	int i, j;
    scanf("%s", S);
    n = strlen(S);
    for(i = 0; i < n; ++ i) bt[S[i] - 'a'].set(i);
    int Q; r1(Q); while(Q --) {
        int opt, l, r;
        r1(opt);
        if(opt == 1) {
            r1(l), scanf("%s", t), -- l;
            bt[S[l] - 'a'].reset(l), bt[(S[l] = t[0]) - 'a'].set(l);
        }
        else {
            r1(l, r), scanf("%s", t);
            -- l, -- r, m = strlen(t);
            ans.set();
            for(i = 0; i < m; ++ i) ans &= (bt[t[i] - 'a'] << (m - i - 1));
            int res = (ans >> (l + m - 1)).count() - (ans >> (r + 1)).count();
            printf("%d\n", max(res, 0));
        }
    }

	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

[Frequency of String - 洛谷](https://www.luogu.com.cn/problem/CF963D)

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
int n, m;
char S[maxn], T[maxn];
bitset<maxn> bt[26], ans;
int p[maxn], tot(0);

signed main() {
	int i, j;
    scanf("%s", S);
    n = strlen(S);
    for(i = 0; i < n; ++ i) bt[S[i] - 'a'].set(i);
    int Q; r1(Q); while(Q --) {
        int k; r1(k), scanf("%s", T);
        m = strlen(T);
        ans.set();
        for(i = 0; i < m; ++ i) ans &= (bt[T[i] - 'a'] << (m - i - 1));
        tot = 0;
        for(i = ans._Find_first(); i != ans.size(); i = ans._Find_next(i)) if(i >= m - 1)
            p[++ tot] = i;
        int ans(2e5);
        if(tot < k) { puts("-1"); continue; }
        for(i = k; i <= tot; ++ i) ans = min(ans, p[i] - p[i - k + 1]);
        printf("%d\n", ans + m);
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
