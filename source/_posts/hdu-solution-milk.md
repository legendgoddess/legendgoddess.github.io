---
title: HDU6580 Milk
date: 2021-09-12 20:56:51
tags:
  - Dp
categories:
  - HDU题解
---

<h3> <center>  HDU6580 Milk </center></h3>

> [HDU 6580 Milk](http://acm.hdu.edu.cn/showproblem.php?pid=6580)

> 校内考试题目，感觉考试的时候没看这题，之后还被恶心了一下 $\dots$。

 
#### 考试的经过：

> 搞 $\tt T3$ 嗯，很好简单二项式反演。好最后转换错了，没了。
>
> 之后 $\tt 30 \ minutes$ 诶呀这个不是网络流题吗？高分暴力呀！！
>
> 之后没写出来，显然网络流不能处理这个东西，及时说每次修改终点那一段的权值。仅仅是样例就能说明费用流显然是直接流最好。这个真的要搞清楚定义，哎呀。
>
> 感觉建图的时候还要稍微改一下不能很随便就直接距离连边之类的。

-----

#### 题解：

我们考虑只有中间的一条线可以走，我们不妨对于每一个终止行进行计算。那么其他的位置肯定是 **进入行的内部再回到中间** 。

不妨对于每一行进行 $\tt Dp$ 保存向左终止和向右终止，向左返回和向右返回的情况。

之后合并的时候使用背包。

每次 $\tt Dp$ 的时候对于每个位置都计算一次贡献，从距离起始点从近到远进行计算即可。

之后每次枚举行，然后记录一下之前的 $\tt Dp$ 。

> 不要忘了离散化。

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
const int maxn = 1e4 + 5;
const int maxm = maxn << 1;
typedef long long ll;
int n, m, K;
struct Node {
    int x, y, t;
    int operator < (const Node &z) const {
        return y < z.y;
    }
}pt[maxn];

const ll inf = 0x3f3f3f3f3f3f3f3f;

vector<ll> merge(const vector<ll>& a, const vector<ll>& b) {
    vector<ll> c(a.size() + b.size() - 1, inf);
    for(int i = 0; (unsigned)i < a.size(); ++ i) {
        for(int j = 0; (unsigned)j < b.size(); ++ j) {
            c[i + j] = min(c[i + j], a[i] + b[j]);
        }
    }
    return c;
}

vector<ll> Solve(const vector<Node>& a, int dis) {
    /*
    a 到 dis 的距离从近到远
    */
    vector<ll> res(a.size(), inf), tmp(a.size(), inf);
    res[0] = (dis == -1) ? 0 : (abs(dis - a[0].y));
    tmp[0] = 0;
    for(int i = 1; (unsigned)i < a.size(); ++ i) {
        int Dis = abs(a[i].y - a[i - 1].y);
        for(int j = 0; j < i; ++ j) tmp[j] += Dis;
        for(int j = i; j >= 1; -- j) tmp[j] = min(tmp[j], a[i].t + tmp[j - 1]);
        for(int j = 0; j <= i; ++ j) {
            res[j] = min(res[j], tmp[j] + (dis == -1 ? 0 : abs(dis - a[i].y)));
        }
    }
    return res;
}

signed main() {
    freopen("milk.in", "r", stdin);
    freopen("milk.out", "w", stdout);
    int i, T(1);
//    r1(T);
    while(T --) {
        r1(n, m, K);
        vector<ll> nc;// 离散化
        nc.push_back(1);
        vector<ll> f[2];
        f[0].resize(K + 1);
        f[1].resize(K + 1);
        for(i = 1; i <= K; ++ i) {
            r1(pt[i].x, pt[i].y, pt[i].t);
            nc.push_back(pt[i].x);
        }
//        printf("%d\n", c.size());
//        puts(" \n --- ");
        sort(nc.begin(), nc.end());
        nc.erase(unique(nc.begin(), nc.end()), nc.end());
        vector<vector<Node>> line;
        line.resize(K + 2);
        vector<ll> ans; ans.resize(K * 2 + 1);
        for(i = 1; i <= K; ++ i) {
            int tmp = lower_bound(nc.begin(), nc.end(), pt[i].x) - nc.begin();
//            printf("tmp = %d\n", tmp);
            line[tmp].push_back(pt[i]);
            ans[i] = inf;
        }
//        puts("CCC");
//        printf("%d\n", line[0].size());
        for(i = 0; (unsigned)i < nc.size(); ++ i) sort(line[i].begin(), line[i].end());
//        puts("ZZZ");
        vector<Node> nl = line[0];
        nl.insert(nl.begin(), (Node) {1, 1, 0});
        f[0] = Solve(nl, (m + 1) >> 1);
        vector<ll> tmp = Solve(nl, -1);
//        printf("%d\n", tmp.size());
        for(int i = 0; (unsigned)i < tmp.size(); ++ i) ans[i] = min(ans[i], tmp[i]);

        for(int _ = 1; (unsigned)_ < nc.size(); ++ _) {
            auto it = lower_bound(line[_].begin(), line[_].end(), (Node){_, (m + 1) >> 1, 0});
            vector<Node> l0(line[_].begin(), it);
            vector<Node> l1(it, line[_].end());

            l0.push_back((Node){_, (m + 1) >> 1, 0});
            reverse(l0.begin(), l0.end());

            l1.insert(l1.begin(), (Node) {_, (m + 1) >> 1, 0});

//            printf("%d %d\n", l0.size(), l1.size());
//            printf("%d\n", line[_].size());
            vector<ll> bl, br, gl, gr;
            bl = Solve(l0, (m + 1) >> 1);
            br = Solve(l1, (m + 1) >> 1);
            gl = Solve(l0, -1);
            gr = Solve(l1, -1);

            gl = merge(br, gl);
            gr = merge(bl, gr);

            vector<ll> ng; ng.resize(gl.size());
            for(i = 0; (unsigned)i < gl.size(); ++ i)
                ng[i] = min(gl[i], gr[i]); // go

            vector<ll> g = merge(f[(_ - 1) & 1], ng); //bk

            f[_ & 1] = merge(f[(_ - 1) & 1], merge(bl, br));
//            printf("%d\n", g.size());
            for(i = 0; (unsigned)i < g.size(); ++ i) {
                g[i] += nc[_] - nc[_ - 1];
                ans[i] = min(ans[i], g[i]);
                f[_ & 1][i] += nc[_] - nc[_ - 1];
            }
        }
        for(i = 1; i <= K; ++ i) printf("%lld%c", ans[i], " \n"[i == K]);
    }
	return 0;
}
```

----

```cpp
#include <bits/stdc++.h>
using namespace std;

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

#define int long long
const int maxn = 2e5 + 5;
const int maxm = maxn << 1;
const int inf = 1e18;
int n, m, K;

struct Node {
    int x, y, t;
    int operator < (const Node &z) const {
        return y < z.y;
    }
}pt[maxn];

vector<int> merge(const vector<int>&a, const vector<int>&b) {
    vector<int> c(a.size() + b.size() - 1, inf);
    for(int i = 0; i < a.size(); ++ i) for(int j = 0; j < b.size(); ++ j)
        c[i + j] = min(c[i + j], a[i] + b[j]);
    return c;
}

/*
1
7 7 2
3 7 1
4 5 1
*/

vector<int> Solve(const vector<Node>& a, int Dis) {
    /*
    钦定第一个 a[0] 是开始位置
    */
    vector<int> res(a.size(), inf); // 选了 i 个位置的最优解
    vector<int> tmp(a.size(), inf);
    res[0] = (Dis == -1 ? 0 : abs(Dis - a[0].y));
    tmp[0] = 0;
    int len = a.size();
    for(int i = 1; i < len; ++ i) {// 钦定每一个位置作为结束节点即可
        for(int j = 0; j < i; ++ j) tmp[j] += abs(a[i].y - a[i - 1].y);
        for(int j = i; j >= 1; -- j) tmp[j] = min(tmp[j], tmp[j - 1] + a[i].t);
        for(int j = 0; j <= i; ++ j) res[j] = min(res[j], tmp[j] + (Dis == -1 ? 0 : abs(a[i].y - Dis)));
    }
    return res;
}

void Solve() {
    int i, j;
    r1(n, m, K);
    int mid = (m + 1) >> 1;
    vector<int> uni;
    for(i = 1; i <= K; ++ i) r1(pt[i].x, pt[i].y, pt[i].t), uni.push_back(pt[i].x);

    uni.push_back(1);
    sort(uni.begin(), uni.end());
    uni.erase(unique(uni.begin(), uni.end()), uni.end());
    int len = uni.size();

    vector<vector<Node> > ln; ln.resize(K + 2);
    for(i = 1; i <= K; ++ i) {
        int pos = lower_bound(uni.begin(), uni.end(), pt[i].x) - uni.begin();
        ln[pos].push_back(pt[i]);
    }

    for(i = 0; i < len; ++ i) sort(ln[i].begin(), ln[i].end());
    vector<int> f[2];
    f[0].clear(), f[1].clear();

    vector<Node> x(ln[0].begin(), ln[0].end());
    x.insert(x.begin(), (Node) {1, 1, 0});
    vector<int> tmp = Solve(x, -1);
    f[0] = Solve(x, mid);
//    printf("%d\n", f[0][0]);

    vector<int> ans(2 * K + 2, inf);
//    printf("%d\n", f[0].size());
//    puts("XXX");
    for(i = 0; i < tmp.size(); ++ i) ans[i] = min(ans[i], tmp[i]);

//    puts("XXX");

    for(i = 1; i < len; ++ i) { // 后置贡献，考虑在当前点停下的贡献
        auto it = lower_bound(ln[i].begin(), ln[i].end(), (Node) {1, mid, 0});
        vector<Node> x(ln[i].begin(), it), y(it, ln[i].end());
        x.push_back((Node) {i, mid, 0});
        reverse(x.begin(), x.end());
        y.insert(y.begin(), (Node) {i, mid, 0});
//        printf("%d : %d %d\n", i, x.size(), y.size());
        vector<int> gl = Solve(x, -1);
        vector<int> bl = Solve(x, mid);
        vector<int> gr = Solve(y, -1);
        vector<int> br = Solve(y, mid);

//        printf("%d : %d ", i, gr.size());
//        if(gr.size() > 1) printf("%d ", gr[1]);
//        puts("");
//        puts("CCC");
        gl = merge(gl, br);
        gr = merge(gr, bl);

        vector<int> tmp;
        tmp.resize(gl.size());
        for(j = 0; j < gl.size(); ++ j) tmp[j] = min(gl[j], gr[j]);

        vector<int> ng = merge(f[(i - 1) & 1], tmp);
//        puts("YYYy");
        f[i & 1] = merge(f[(i - 1) & 1], merge(bl, br));
//        printf("Dis = %d\n", uni[i] - uni[i - 1]);
        for(j = 0; j < ng.size(); ++ j) {
            ng[j] += uni[i] - uni[i - 1];
            f[i & 1][j] += uni[i] - uni[i - 1];
            ans[j] = min(ans[j], ng[j]);
        }

    }

    for(i = 1; i <= K; ++ i) printf("%lld%c", ans[i], " \n"[i == K]);
}

/*
1
7 7 2
3 7 1
4 5 1
*/

signed main() {
//    freopen("milk.in", "r", stdin);
//    freopen("milk.out", "w", stdout);
    int T(1);
    r1(T);
    while(T --) Solve();
	return 0;
}

```



