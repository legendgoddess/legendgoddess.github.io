---
title: CF383E Vowels
date: 2021-09-14 19:08:39
tags:
  - 数论
categories:
  - CF题解
---

<h3><center>CF383E Vowels</center></h3>

> [CF383E Vowels](https://www.luogu.com.cn/problem/CF383E)。
>
> emmm 可以自己想出来的简单题。

---

就是考虑容斥，也就是 $A \cup B = A + B - A \cap B$。三个数的情况也是同理。发现每个字符串的长度 $= 3$ 所以我们可以直接对于集合大小是 $3$ 的进行暴力容斥计算。

之后我们考虑集合的合并，直接进行状压 $\tt Dp$，我们考虑删除最前面和最后面的那一位的贡献之和减去同时删除的贡献即可。

----

或者说我们可以考虑更好写的情况。我们发现合法的集合肯定是至少包含其中一个位置的。

**对于至少可以通过取补集转换成全部不包含**。

对于一个不合法的集合肯定是所有位置都在这里的，那么后者完全可以使用高位前缀和进行维护，我们对于每一个补集进行计算即可。

> 显然所有的集合都被计算过了。因为两个交集为空，并集为全集的集合会互相计算。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
//#define Getmod

#ifdef Fread
char buf[1 << 21], *iS, *iT;
#define gc() (iS == iT ? (iT = (iS = buf) + fread (buf, 1, 1 << 21, stdin), (iS == iT ? EOF : *iS ++)) : *iS ++)
#define getchar gc
#endif // Fread

template <typename T>
void r1(T &x) {
	x = 0;
	char c(getchar());
	int f(1);
	for(; c < '0' || c > '9'; c = getchar()) if(c == '-') f = -1;
	for(; '0' <= c && c <= '9';c = getchar()) x = (x * 10) + (c ^ 48);
	x *= f;
}

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

//#define int long long
const int maxn = 1e4 + 5;
const int maxm = (1 << 24);

char s[10];
int f[maxm], n;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    int All(1 << 24); -- All;
    for(i = 1; i <= n; ++ i) {
        scanf("%s", s + 1);
        int tmp(0);
        for(j = 1; j <= 3; ++ j) if(s[j] < 'y') {
            tmp |= (1 << (s[j] - 'a'));
        }
        ++ f[tmp];
    }
//    printf("%d\n", (~5) & (7));
    for(i = 0; i < 24; ++ i) {
        for(int S = 0; S < (1 << 24); ++ S) if((S >> i) & 1) {
            f[S] += f[S ^ (1 << i)];
        }
    }
    int ans(0);
    for(int S = 0; S < (1 << 24); ++ S) {
        ans ^= (n - f[S]) * (n - f[S]);
    }
    printf("%d\n", ans);
	return 0;
}
```




