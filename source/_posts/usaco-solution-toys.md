---
title: "[USACO08NOV]Toys G"
date: 2021-09-09 19:02:00
tags:
  - Dp
  - 贪心
categories:
  - USACO题解
---

### [[USACO08NOV]Toys G](https://www.luogu.com.cn/problem/P2917)

首先可以网络流建图，但是因为 $n$ 的范围很大，所以我们考虑换一种方法。

首先考虑网络流建图的时候我们是考虑拆点之后进行跑流量，对于所有满流的情况我们计算一个最小费用。

---

我们对于这种情况不妨进行二分答案之后进行判断，显然我们二分的不能直接是答案。我们考虑那个条件我们知道了之后可以进行贪心或者 $\tt Dp$。总共就那么几个条件，只有购买了多少个是不知道的。那么我们就二分买了几个玩具。

---

简单思考可以发现对于买的玩具数量和花费是一个二次函数，下凸，进行三分。

具体贪心：

首先如果一个方案时间又短又优秀显然只用这个方案，不然两种方案肯定是一个时间短花费多，另一个反之。我们考虑用队列维护又多少没有消毒的，然后每次优先通过时间长的进行消毒，不然我们就使用时间短的进行消毒。

> 这里有一个问题如果放到了时间短的里面但是没有被使用，之后又可以放到时间长的里面了。

对于这个疑问，我们可以每次将时间短的来更新一下时间长的，之后每次将贡献放到被使用的时候计算即可。

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

//#define int long long
const int maxn = 1e5 + 5;
const int maxm = maxn << 1;

int n, v[maxn];

int fd, fc, sd, sc, Pr;

#define id first
#define val second
deque<pair<int, int>> cf, cs, vir;

const int inf = 1e9;

int calc(int x) {
    cf.clear(), cs.clear(), vir.clear();
    int ans = x * Pr;
    for(int i = 1; i <= n; ++ i) {
        while(!vir.empty() && vir.front().id <= i - fd) {
            cf.push_back(vir.front()), vir.pop_front();
        }
        while(!cf.empty() && cf.front().id <= i - sd) {
            cs.push_back(cf.front()), cf.pop_front();
        }
        int left(v[i]);
        int tmp = min(x, left);
        x -= tmp, left -= tmp;
        while(left && !cs.empty()) {
            tmp = min(left, cs.back().val);
            ans += tmp * sc, left -= tmp, cs.back().val -= tmp;
            if(!cs.back().val) cs.pop_back();
        }
//        printf("i = %d\n", i);
        while(left && !cf.empty()) {
//            printf("%d %d\n", left, cf.back().val);
            tmp = min(left, cf.back().val);
//            printf("tmp = %d\n", tmp);
            ans += tmp * fc, left -= tmp, cf.back().val -= tmp;
            if(!cf.back().val) cf.pop_back();
        }
        if(left) return inf;
        vir.push_back(make_pair(i, v[i]));
    }
    return ans;
}

int Work() {
    int l(0), r(0);
    for(int i = 1; i <= n; ++ i) r += v[i];
    while(r - l > 2) {
//        printf("(%d, %d)\n", l, r);
        int m1 = (l * 2 + r) / 3, m2 = (l + r * 2 + 2) / 3;
        int f1 = calc(m1);
//        puts("XXX");
        int f2 = calc(m2);
        if(f1 >= f2) l = m1;
        else r = m2;
    }
    int ans(inf);
    for(int i = l; i <= r; ++ i) ans = min(ans, calc(i));
    return ans;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    r1(fd, sd, fc, sc);
    if(fd > sd) {
        swap(fd, sd), swap(fc, sc);
    }
    if(sc > fc) {
        sc = fc, sd = fd;
    }
    r1(Pr);
    for(i = 1; i <= n; ++ i) r1(v[i]);
    printf("%d\n", Work());
	return 0;
}

```




