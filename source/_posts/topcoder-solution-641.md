---
title: Topcoder SRM 641 BitToggler
date: 2021-08-21 14:47:52
tags:
  - 期望
  - 数论
  - 矩阵
  - Dp
categories:
  - TOPCODER题解
---


### [Topcoder SRM 641 BitToggler](https://vjudge.net/problem/TopCoder-13328)

**题目大意：**

给定一个长度为 $n$ 的 $01$ 序列，给定当前位置，每一次会等概率随机走到一个除了当前点的任意位置，并且取反走到的当前点，其花费两个点的距离。求期望多少花费能让整个序列的所有数相同。

因为期望具有线性，所以我们考虑对于全部的操作都是由若干条 $(u, v)$ 边构成的，所以我们对于一条边考虑在最终答案上被计算了几次。

但是发现对于一条边的两个点 $(u, v)$ 我们还需要保存其他点的信息。

其实不然，容易想到找到当前有多少个点是 $1$ 即可。

其次我们还需要知道 $a_u, a_v$ 所以我们可以设置状态。

$f(a, 0/1, 0/1)$ 表示剩下 $n - 2$ 个点中有多少个点是 $1$。点 $i, j$ 的颜色信息。这个表示 $i \to j$ 这条边在整个序列到达结束状态的时候使用了几次。

我们考虑什么时候会产生 $1$ 的路径贡献，也就是一开始 $i$ 是起点的时候，这是一开始的初值。

然后我们考虑转移：

- 从 $!a_j$ 转移过来，这个时候 $j$ 需要是起点。
- 从 $!a_i$ 转移过来。
- 从点权为 $0$ 的点转移过来。
- 从点权为 $1$ 的点转移过来。

但是因为要考虑多组数据，我们起始点的颜色状态其实是不确定的。我们必须增加状态来表示起点。

最终得到的状态是 $f(a, 0/1, 0/1, 0/1/2/3)$ 前面相同。

```0```：点 $i$ 是起点。 

```1```：点 $j$ 是起点。

```2```：起点权值是 $0$。

```3```：起点权值是 $1$。

之后直接暴力高斯消元就好了。

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

//#define int long long
const int maxn = 330 + 5;

int n, m, Q;
double mt[maxn][maxn], sum;
vector<double> ans;


void init() {
    m = (n - 1) * 16;
    double p = 1.0 / n;
//    printf("p = %.3lf\n", p);
    for(int i = 0; i <= 1; ++ i) {
        for(int j = 0; j <= 1; ++ j) {
            for(int a = 0; a <= n - 2; ++ a) {
                int s = (a * 4 + i * 2 + j) * 4;
                if(s == 0 || s == (n - 2) * 16 + 12) {// 0, 1
                    for(int t = 0; t <= 3; ++ t) mt[s + t][s + t] = 1;
                    continue;
                }
                for(int t = 0; t <= 3; ++ t) {
                    mt[s + t][s + t] = 1;
                    mt[s + t][s ^ 8] = -p;
                    mt[s + t][(s ^ 4) + 1] = -p;
                    if(a) mt[s + t][s - 16 + 2] = -p * a; // 0
                    if(a < n - 2) mt[s + t][s + 16 + 3] = -(n - 2 - a) * p; // 1
//                    puts("ZZZ");
                }
                mt[s][m] = p; //i is beginning
            }
        }
    }
    const double eps = 1e-8;
    for(int i = 1; i <= m - 1; ++ i) {
        int pos = i;
        while(pos < m && fabs(mt[pos][i]) < eps) ++ pos;
        if(pos >= m) continue;
        for(int j = 1; j <= m; ++ j) swap(mt[pos][j], mt[i][j]);
        for(int j = 1; j <= m - 1; ++ j) if(i != j && mt[j][i]) {
            double s = mt[j][i] / mt[i][i];
            for(int k = 1; k <= m; ++ k)
                mt[j][k] -= s * mt[i][k];
        }
    }
//    for(int i = 1; i <= m - 1; ++ i) {
//        for(int j = 1; j <= m; ++ j) printf("%.3lf ", mt[i][j]);
//        puts("");
//    }
}

class BitToggler {
public:
    vector<double> expectation(int T, vector<string> s, vector<int> pos) {
        int Q = s.size();
        n = T;
        init();
        for(int x = 0; x < Q; ++ x) {
            double sum = 0; int num(0);
            for(int i = 0; i < n; ++ i) num += (s[x][i] == '1');
            for(int i = 0; i < n; ++ i) {
                for(int j = 0; j < n; ++ j) if(i != j) {
                    int k(3);
                    if(i == pos[x]) k = 0;
                    else if(j == pos[x]) k = 1;
                    else if(s[x][pos[x]] == '0') k = 2;
                    int xi = s[x][i] - '0', xj = s[x][j] - '0';
                    int S = ((num - xi - xj) * 4 + xi * 2 + xj) * 4 + k;
                    double tmp = mt[S][m] / mt[S][S] * fabs(i - j);
                    sum += tmp;
//                    printf("i = %d, j = %d, tmp = %.3lf\n", i, j, tmp);
                }
            }
            ans.push_back(sum);
        }
        return ans;
    }
};

//signed main() {
//    vector<string> s;
//    s.push_back("1000");
//    s.push_back("0010");
//    s.push_back("0011");
//    s.push_back("1010");
//    vector<int> pos;
//    pos.push_back(0);
//    pos.push_back(1);
//    pos.push_back(2);
//    pos.push_back(3);
//    BitToggler T;
//    vector<double> ans = T.expectation(4, s, pos);
//    for(auto v : ans) printf("%lf\n", v);
//    return 0;
//}

```




