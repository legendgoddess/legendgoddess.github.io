---
title: 切比雪夫距离浅谈
date: 2021-09-11 16:42:48
tags:
  - 数论
categories:
  - 算法
---

<h3> <center> 切比雪夫距离和曼哈顿距离 </center> </h3>

众所周知两个点 $(x_1, y_1), (x_2, y_2)$ 的曼哈顿距离是 $|x_1 - x_2| + |y_1 - y_2|$。

显然我们可以通过不等式去掉绝对值 $\max(|x_1 + y_1 + x_2 + y_2|, |x_1 + y_1 - (x_2 + y_2)|)$。

切比雪夫距离就是 $\max(|x_1 - x_2|, |y_1 - y_2|)$。

然后我们发现这两个格式很像，事实上确实是可以转换的：

- 曼哈顿距离 到 切比雪夫距离 ： $(x, y) \to (x + y, x -y )$。
- 切比雪夫距离  到 曼哈顿距离 ： $(x, y) \to (\frac{x + y}{2}, \frac{x - y}{2})$。

**注意：** 可能 $\frac{x + y}{2}$ 不一定是整数，那么我们不妨考虑在四周枚举一下。

---

[P3964 [TJOI2013]松鼠聚会](https://www.luogu.com.cn/problem/P3964)

[P3439 [POI2006]MAG-Warehouse](https://www.luogu.com.cn/problem/P3439)

> 这题就是经典的不一定是整数，我们要在周围的点分类讨论一下。

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

typedef long long int64;

int64 sumx, sumy, sum;

struct Node {
    int64 x, y;
    int64 val;
    Node(void) : x(0), y(0), val(0) {}
    void init() {
        int nx, ny;
        r1(nx), r1(ny);
        x = (nx + ny), y = (nx - ny);
        sumx += x, sumy += y;
    }
}a[maxn];

int n;

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) a[i].init();
    sort(a + 1, a + n + 1, [&] (const Node &a, const Node &b) {
        return a.x < b.x;
    });
//    printf("%lld\n", sumx);
    for(sum = 0, i = 1; i <= n; ++ i) {
        a[i].val += (i - 1) * a[i].x - sum;
        a[i].val += (sumx - sum - a[i].x) - (n - i) * a[i].x;
        sum += a[i].x;
    }
    sort(a + 1, a + n + 1, [&] (const Node &a, const Node &b) {
        return a.y < b.y;
    });
    for(sum = 0, i = 1; i <= n; ++ i) {
        a[i].val += (i - 1) * a[i].y - sum;
        a[i].val += (sumy - sum - a[i].y) - (n - i) * a[i].y;
        sum += a[i].y;
    }
//    for(i = 1; i <= n; ++ i) printf("%d : %lld, %lld\n", i, a[i].val, a[i].y);
    int64 ans(1e18);
    for(i = 1; i <= n; ++ i) if(a[i].val < ans) ans = a[i].val;
    printf("%lld\n", ans >> 1);
	return 0;
}

```

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

int n;

__int128 min(__int128 x, __int128 y) { return x < y ? x : y; }
__int128 abs(__int128 x) { return x < 0 ? -x : x; }

__int128 sx, sy, st;

struct Node {
    __int128 x, y, t;
    Node(void) : x(0), y(0), t(0) {}
    void init() {
        __int128 nx, ny;
        r1(nx, ny, t);
        x = (nx + ny), y = (nx - ny);
        sx += x * t, sy += y * t, st += t;
    }
}a[maxn];

void write(__int128 x) {
    vector<char> s; s.clear();
    while(x) s.push_back((char)('0' + x % 10)), x /= 10;
    reverse(s.begin(), s.end());
    for(auto v : s) putchar(v);
}

void Out(__int128 x, __int128 y) {
    write((x + y) >> 1), putchar(' '), write((x - y) >> 1), puts(""), void();
}

__int128 calc(__int128 x, __int128 y) {
    __int128 res(0);
    for(int i = 1; i <= n; ++ i) {
        res += ( abs(x - a[i].x) + abs(y - a[i].y) ) * a[i].t;
    }
    return res;
}

void Work(__int128 x,__int128 y) {
    int i, j;
    if((x & 1) == (y & 1)) return Out(x, y);
    __int128 s1 = calc(x, y + 1);
    __int128 s2 = calc(x - 1, y);
    __int128 s3 = calc(x, y - 1);
    __int128 s4 = calc(x + 1, y);
    __int128 ans = min(min(s1, s2), min(s3, s4));
    if(ans == s1) return Out(x, y + 1);
    if(ans == s2) return Out(x - 1, y);
    if(ans == s3) return Out(x, y - 1);
    if(ans == s4) return Out(x + 1, y);
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) a[i].init();
    sort(a + 1, a + n + 1, [&] (const Node &a, const Node &b) {
            return a.x < b.x;
         });
    __int128 sum, ct, mnx, mny, x, y;
    mnx = 0x3f3f3f3f3f3f3f3f;
    mny = mnx = (mnx * mnx);
    for(sum = ct = 0, i = 1; i <= n; ++ i) {
        __int128 tmp(0);
        tmp += a[i].x * ct - sum;
        tmp += - (st - ct - a[i].t) * a[i].x + (sx - a[i].t * a[i].x - sum);
        if(tmp < mnx) mnx = tmp, x = a[i].x;
        sum += a[i].x * a[i].t;
        ct += a[i].t;
    }
    sort(a + 1, a + n + 1, [&] (const Node &a, const Node &b) {
            return a.y < b.y;
         });
    for(sum = ct = 0, i = 1; i <= n; ++ i) {
        __int128 tmp(0);
        tmp += a[i].y * ct - sum;
        tmp += - (st - ct - a[i].t) * a[i].y + (sy - a[i].t * a[i].y - sum);
        if(tmp < mny) mny = tmp, y = a[i].y;
        sum += a[i].y * a[i].t;
        ct += a[i].t;
    }

    Work(x, y);

	return 0;
}

```




