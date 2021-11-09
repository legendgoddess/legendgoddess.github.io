---
title: CF865E Hex Dyslexia
date: 2021-08-14 09:38:23
tags:
  - 图论
  - Dp
categories:
  - CF题解
---


[CF865E](https://codeforces.com/problemset/problem/865/E)

首先考虑差的性质，也就是 $S = \sum_{i} a_i - b_i$ 我们会发现如果没有进位这个值就是 $0$，不然如果有进位那么每一位会多出来 $15$ 的贡献，可以发现合法的情况是 $S \equiv 0 \pmod {15}$。

> 不然就是不合法的。

然后我们需要钦定有多少位是进位的，复杂度是 $\binom{13}{6}$ 可以接受的样子。

又因为 $b$ 是 $a$ 的置换，本质上我们可以看成 $a_i - a_{p_i}$。我们不妨将这种情况连边 $i \to p_i$。可以连接成若干个环。对于每一个点的权值 $w_i = a_i - a_{p_i}$。但是对于每一个环我们统一进行处理会更加方便，也就是将整个环 $-1$ 直到出现 $0$。将两个 $0$ 点进行连边即可。

我们进行计算的时候因为对于图上来说，我们如果钦定了一个开始点，剩余的点本身就是一个差分，我们要还原之前的值就是前缀和。

我们再考虑一下 $S$ 的性质，发现 $15 = 16 - 1$ 也就是说明 $16 \times \frac{S}{15} - \frac{S}{15} = S$ 所以我们可以让 $\frac{S}{15}$ 作为一个答案，然后 $b$ 的最高位是 $0$。 如果 $S$ 的最高位是 $f$ 显然 $b$ 的最高位也是 $0$。

我们考虑进行一个 $Dp​$。因为已经知道 $b​$ 的最高位是 $0​$，所以 $a​$ 的最高位直接就可以是 $S_{mx}​$。然后剩余的位置本质上和求 $a​$ 是一样的，我们直接通过前缀和进行计算即可。

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

#ifdef Getmod
const int mod  = 1e9 + 7;
template <int mod>
struct typemod {
    int z;
    typemod(int a = 0) : z(a) {}
    inline int inc(int a,int b) const {return a += b - mod, a + ((a >> 31) & mod);}
    inline int dec(int a,int b) const {return a -= b, a + ((a >> 31) & mod);}
    inline int mul(int a,int b) const {return 1ll * a * b % mod;}
    typemod<mod> operator + (const typemod<mod> &x) const {return typemod(inc(z, x.z));}
    typemod<mod> operator - (const typemod<mod> &x) const {return typemod(dec(z, x.z));}
    typemod<mod> operator * (const typemod<mod> &x) const {return typemod(mul(z, x.z));}
    typemod<mod>& operator += (const typemod<mod> &x) {*this = *this + x; return *this;}
    typemod<mod>& operator -= (const typemod<mod> &x) {*this = *this - x; return *this;}
    typemod<mod>& operator *= (const typemod<mod> &x) {*this = *this * x; return *this;}
    int operator == (const typemod<mod> &x) const {return x.z == z;}
    int operator != (const typemod<mod> &x) const {return x.z != z;}
};
typedef typemod<mod> Tm;
#endif

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

#define int long long
const int maxn = (1 << 17) + 5;
const int maxm = maxn << 1;
const int inf = 0x3f3f3f3f3f3f3f3f;
int ans(inf);
int d[20];
int lg[maxn], f[maxn], sum[maxn];

char c[20];

int n;

void Work() {
    int z = (1 << (n - 1)) - 1;
    memset(f, 0x3f, sizeof(f));
    auto lowbit = [&] (int x) {return x & -x;};
    sum[0] = d[n - 1]; f[0] = 0;
    for(int i = 1; i <= z; ++ i)
        sum[i] = sum[i - lowbit(i)] + d[lg[lowbit(i)]];
    if(sum[z] < 0 || sum[z] > 15) return ;
    for(int i = 0; i <= z; ++ i) {
        if(sum[i] < 0 || sum[i] > 15) continue;
        for(int j = 0; j < n; ++ j) if(!((i >> j) & 1)) {
            f[i | (1 << j)] = min(f[i | (1 << j)], f[i] + (sum[i] << (j << 2)));
        }
    }
    ans = min(ans, f[z]);
}

void dfs(int u,int ct) {
    if(u == n - 1) {
        if(!ct) Work();
        return ;
    }
    if(n - u > ct) dfs(u + 1, ct);
    if(ct) {
        d[u] -= 16, d[u + 1] ++;
        dfs(u + 1, ct - 1);
        d[u] += 16, d[u + 1] --;
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j, sum(0);
    scanf("%s", c), n = strlen(c);
    for(i = 0; i < n; ++ i) {
        if(isdigit(c[i])) d[i] = c[i] - '0';
        else d[i] = c[i] - 'a' + 10;
        sum += d[i];
    }
    reverse(d, d + n);
    for(i = 0; i < n; ++ i) lg[1 << i] = i;
//    puts("ZZZ");

    if(sum % 15) return puts("NO"), 0;
    dfs(0, sum / 15);

    if(ans == inf) return puts("NO"), 0;
    else {
//            printf("ans = %lld\n", ans);
        vector<int> st;
        while(ans) st.push_back( ans % 16 ), ans /= 16;
        while(st.size() < n) st.push_back(0);
        reverse(st.begin(), st.end());
        vector<char> vis(20, 0);
        for(i = 0; i <= 9; ++ i) vis[i] = '0' + i;
        for(i = 10; i < 16; ++ i) vis[i] = i - 10 + 'a';
        for(auto v : st) putchar(vis[v]);
        puts("");
//        for(auto v : st) printf("%lld ", v);
//        puts("");
    }

	return 0;
}
```




