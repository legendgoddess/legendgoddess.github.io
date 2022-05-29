---
title: "P4548 [CTSC2006]歌唱王国"
date: 2022-5-29 20:17:00
tags:
  - 生成函数
  - 字符串
categories:
  - 洛谷题解
---

我们考虑最终答案为 $i$ 的概率为 $f_i$。

考虑转移的时候肯定是通过 $\tt border$ 进行转移，这样就可以通过 $\tt Dp$ 进行解决。

考虑使用一种另外的方法，我们思考 $f_i$ 肯定是通过一个不合法的情况进行转移的，我们考虑设 $g_i$ 表示长度为 $i$ 但是没有结束。

通过生成函数表示，显然有等式 $ans = F'(1)$。且 $F$ 和 $G$ 的关系是 $F + G = Gx + 1$，就是考虑之后再加入一个字符的贡献。

之后考虑先前 $\tt Dp$ 的 $\tt border$ 转移，显然对于 $G$ 加入某些字符是可以使得其满足条件的。

假设加入了字符串 $S$ 要么 $S$ 就是要求的字符串，要么就是因为之前已经有过一部分了，不妨考虑我们直接加入要求字符串，之后减去多算的，因为多算的部分肯定是 $\tt border$。

> 考虑已经有的部分是 $A$ 后面的部分是 $B$ 满足 $A + B = L$ 那么我们有 $L - B = A$ 也就是说 $A$ 满足前缀等于后缀，所以是 $\tt border$。

有等式 $G \times (\frac{1}{m}x)^L = \sum_{i =1} ^ L a_i F \times (\frac{1}{m}x)^{L - i}$。

其中 $a_i$ 表示长度为 $i$ 是不是 $\tt border$。

我们梳理一下：
$$
F + G = Gx + 1 \\\\
G \times (\frac{1}{m}x)^L = \sum_{i =1} ^ L a_i F \times (\frac{1}{m}x)^{L - i}
$$
我们最终的答案是 $F’(1)$ 考虑表示一下 $F' = G'x + G - G'$。

也就是说 $F'(1) = G(1)$。

发现下面式子循环的上限制是 $L$，是否可以直接求出 $G$。

考虑带入得到 $G(1) \times \frac{1}{m^L} = \sum_{i = 1} ^ L a_i F(1) \times \frac{1}{m^{L - i}}$。

我们发现 $F(1) = 1$ 因为是算概率，所以全部概率和就是 $1$。

我们有 $G(1) = \sum_{i = 1} ^ L a_i m^i$。

直接求出 $a$ 就行了。

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;
namespace Legendgod {
	namespace Read {
		#define Fread
		#ifdef Fread
		const int Siz = (1 << 20) + 5;
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

constexpr int maxn = 1e5 + 5;
constexpr int mod = 1e4;
int n, m, pw[maxn], nxt[maxn], s[maxn];

struct Fastmod {
    typedef unsigned long long ULL;
    typedef __uint128_t LLL;
    ULL b, m;
    void init(ULL b) { this->b = b, m = ULL((LLL(1) << 64) / b); }
    ULL operator() (ULL a) {
        ULL q = (ULL)((LLL(m) * a) >> 64);
        a -= q * b;
        return a >= b ? a - b : a;
    }
}M;

inline void border() {
    for(int i = 2, j = 0; i <= m; ++ i) {
        while(j && s[j + 1] != s[i]) j = nxt[j];
        if(s[j + 1] == s[i]) ++ j;
        nxt[i] = j;
    }
}

signed main() {
	int i, j;
    r1(n, m);
    M.init(mod);
    n %= mod;
    for(pw[0] = 1, i = 1; i < maxn; ++ i) pw[i] = M(pw[i - 1] * n);
    for(int t = m, ans = 0; t >= 1; -- t) {
        r1(m), ans = 0;
        for(i = 1; i <= m; ++ i) r1(s[i]);
        border();
        for(i = m; i >= 1; i = nxt[i]) ans += pw[i];
        printf("%0*d\n", 4, ans % mod);
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

