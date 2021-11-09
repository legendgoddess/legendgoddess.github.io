---
title: CF1562E Rescue Niwen!
date: 2021-09-13 21:16:46 
tags:
  - Dp
  - 贪心
categories:
  - CF题解
---

<h3> <center> CF1562E Rescue Niwen! </center></h3> 

>  [CF1562E Rescue Niwen!](https://www.luogu.com.cn/problem/CF1562E)

> 应该是经典 $\tt Dp$ 题目的变种。

字符串 $\tt Lis$ 直接做复杂度是 $O(n^3)$ 的。我们来找寻一下这个的性质。也就是考虑对于一个字符串如果选择了他，肯定可以选择其之后的所有后缀，答案肯定会更优的。

那么我们的 $\tt Lis$ 可以枚举每个位置的起点，如果能加就肯定是直接加了。

- $s_i > s_j$ 肯定是直接加入了，别忘记加上 $j$ 后缀的贡献。
- $s_i = s_j, s_{i + lcp(i, j)} > s_{j + lcp(i, j)}$ 同样加入贡献，但是为了保证合法我们的 $i$ 肯定需要选之后的 $lcp(i, j)$ 个字符来保证可以接到 $j$ 上。

但是我们第二种方法不一定更优，我们钦定一下转移的顺序从小到大进行转移，可以发现后缀是变少的，那么选了 $j$ 的后缀肯定不会比选了 $i$ 的后缀更劣。

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
const int maxn = 5e3 + 5;
const int maxm = maxn << 1;

int n;
char s[maxn];

void Solve() {
    int i, j;
    r1(n);
    scanf("%s", s + 1);
    vector<vector<int> > lcp(n + 2, vector<int>(n + 2, 0));
    for(i = n; i >= 2; -- i) {
        for(j = 1; j < i; ++ j) {
            if(s[i] == s[j])
                lcp[i][j] = lcp[i + 1][j + 1] + 1;
        }
    }
//    for(i = 1; i <= n; ++ i) for(j = 1; j < i; ++ j) printf("lcp[%d][%d] = %d\n", i, j, lcp[i][j]);
    vector<int> f(n + 2, 0);
    int ans(n);
    for(i = 1; i <= n; ++ i) {
        f[i] = n - i + 1;
        for(j = 1; j < i; ++ j) {
            if(s[i] > s[j])
                f[i] = max(f[i], f[j] + n - i + 1);
            else if(s[i] == s[j] && i + lcp[i][j] <= n && s[i + lcp[i][j]] > s[j + lcp[i][j]]) {
                f[i] = max(f[i], f[j] + n - i - lcp[i][j] + 1);
            }
        }
        ans = max(ans, f[i]);
    }
    printf("%d\n", ans);
    return ;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, T;
    r1(T);
    while(T --) Solve();
	return 0;
}

```






