---
title: 浅谈欧拉路径，欧拉回路，以及有向图欧拉回路的计数
date: 2021-10-21 21:35:45
tags: 
  - 欧拉回路
  - 图论
categories:
  - 算法
---

<h3><center>浅谈欧拉路径，欧拉回路</center></h3>

 
### 引入

前有哈密顿路径，表示经过每个点恰好一次，现有欧拉路径，表示经过每条边恰好一次。

许多题目重要的是建模，往往最浅的建模就是点之间的连边，表示可以到达。如果说需要满足到达每个点一次，这就变成了 $\tt NPC$ 问题。但是我们往往可以将一个信息拆分成若干个信息，变成边之间的关系，这样就有多项式复杂度的解法，同样这个是可以求方案的。

本篇会向读者介绍欧拉路径和欧拉回路以及其判定方法，还有常用的套路。

------

### 欧拉图

具有欧拉回路的图叫欧拉图，如果只有欧拉路径就叫做半欧拉图。

------

### 欧拉路径

#### 定义：

经过每条边恰好一次，起点和终点不一定相同。

#### 无向图

每个点的度数都是偶数，或者只有两个节点度数是奇数。

#### 有向图

设一个点的权值 $v_i$ 表示其出度减去入度。

那么存在的欧拉路径的条件是 $v_i = 0$ 或者同时存在一个 $v_i = 1, v_j = -1, i \ne j$。

------

### 欧拉回路

#### 定义

经过每条边恰好一次，起点和终点相同。

#### 无向图

每个点的度数都是偶数。

#### 有向图

设一个点的权值 $v_i$ 表示其出度减去入度。

那么存在的欧拉路径的条件是 $v_i = 0$。

------

### 具体实现

如果说对于一条路径起点和终点是不同的，作为开始的节点需要是出度较大的节点。

我们考虑直接进行暴力 $\tt dfs$，每次删除一条边。在遍历完其相邻的所有点之后将当前点加入答案。

复杂度是 $O(n + m)$ 的。

```cpp
void dfs(int p) {
    while(!vc[p].empty()) {
        int v = vc[p].back(); vc[p].pop_back();
        dfs(v);
    }
    ans[++ ed] = p;
}   
```

为什么要最后加入当前点呢？

如果说出现环的情况，而且我们对于当前点 $u$ 并不是第一次遍历环，那么直接记录答案显然是不行的。

但是我们可以先走这个环，再走链。这种时候就可以考虑最后加入点，这样就是倒着走，环并不会影响答案。

> 读者可以尝试画画图，因为笔者个人博客~~从来不放图~~，请见谅。

因为这样我们的答案是反着的，所以我们可以翻转一下数组。

> 之后的答案都指翻转数组之后的。

------

### 套路

#### 字典序要求

- 字典序最小：贪心走最小的点即可。
- 字典序最大：贪心走最大的点即可。

------

#### 拆点成边

<h5>例题1</h5>

求一个最短的字符串，使得其包含所有的 $n$ 位 $k$ 进制数。

最好的方法就是考虑每次增加一个字符，但是显然直接来做是不方便的。

可能会考虑对于每一个 $k$ 进制数，其能到达的数连边加边权，边权是需要的字符数。

这样转换只能用 $2^x$ 指数级暴力，状压来写。

我们考虑将每个 $n$ 位数，拆分成两个 $n - 1$ 位的数。 $(a_1a_2a_3a_4a_5\dots a_{n - 1})_k \to (a_2 a_3 a_4 a_5 a_6 \dots a_n)_k$ 进行连边。

这样拆出来的每条边都需要经过恰好一次，每次正好是增加一个字符，可以保证答案最小。	

如何保证有解？

> 显然对于最终的一个字符串肯定能拆分成若干个上述形式的 $n - 1$ 位的数。
>
> 因为字符串长度是最小的，所以两个 $n - 1$ 位的数之间的边不会重复经过。
>
> 别忘了是欧拉路径，所以经过多次同一个点是合法的。

<h5>更好的理解</h5>

考虑最终答案字符串的更新过程，也就是每次选取当前区间的 $n$ 的字符进行匹配。

那么显然对于上一个区间更新到这个区间本质上就是$\large\color{red}\text{去掉上一个字符，加上当前的字符}$。

> 显然对于一个状态我们并不需要长度为 $n$ 的区间，而是长度为 $n-  1$ 的区间。

那么相同的部分就是中间的 $n - 2$ 个字符，那么上述的东西可以看做一个状态的转移，从一个 $n - 1$ 长度的区间转移到另外一个长度为 $n - 1$ 的区间。总共有多少个可以转移的区间呢？

对于每一个长度为 $n$ 的数，正好意味着这样的转移。

之后直接进行跑欧拉回路即可。

<h5>例题2</h5>

设一种拆分为对于一个字符串取出其所有长度为 $k$ 连续子串。

给定 $n$ 个长度为 $k$ 的字符串，问能否复原一个长度为 $n + k - 1$ 的字符串，构造任意方案即可。

考虑一下直接将字符串作为点，还是一个哈密顿回路。

那么看到这个问题我们不妨将一个长度为 $k$ 的字符串变成两个长度为 $k - 1$ 的字符串进行连边。

同样也可以考虑最终字符串的转移，对于每个长度为 $k$ 的区间可以拆分成两个长度为 $k - 1$ 的区间。

每个字符串意味着这样的转移。

进行欧拉路径的处理即可。

> - 特判图不连通。

------

### Best 定理

#### 限制

$\huge\color{red}\text{必须是欧拉图}$！！！

------

<h4>前置：矩阵树定理</h4>

求一张图的生成树个数。

设 $D(G)$ 是度数矩阵，$A(G)$ 是邻接矩阵，拉普拉斯矩阵 $L(G) = D(G) - A(G)$。

> 重边也可以计算，但是自环不计邻接矩阵但是计	入度数。

然后随意去掉行号和列号相同的一行一列，得到的就是 $n - 1$ 阶主子式，求出其行列式就是方案数。

<h5>有向图</h5>

- 如果说是求根向树形图，那么度数矩阵就是出度。
- 如果说是求叶向树形图，那么度数矩阵就是入度。

- 任意相同的行和列得到的 $n - 1$ 阶主子式。
- 如果求这样的树形图的所有生成树，对于每一个根进行计数即可。

------

其实上面那个东西的行列式其实就是 $\sum_{T \in Tree} \prod_{w \in Edge} w$。

> 也就是每个生成树的边权的乘积。

所以如果要求上面的那个东西，将邻接矩阵 $A_{i, j}$ 定义成 $i \to j$ 的边权即可。

------

#### 具体部分

如果图 $G$ 是有向欧拉图，那么 $G$ 不同的欧拉回路总数 $ec(G)$ 是：
$$
ec(G) = t^{root}(G, k) \prod_{v \in V}(deg(v) - 1) !
$$
其中 $t^{root}(G, k)$ 表示根向树形图的方案数，其中 $k$ 是任意的根。

> 本质上对于任意的根其欧拉回路的总数都是相同的。

因为是欧拉回路，所以对于一个点的入度和出度是相同的。

> 无向图是 $\tt NPC$ 问题。

<h5>一些细节</h5>

- 图是否联通。
- 欧拉回路本质上就是若干个环构成，如果说环的顺序不同得到的方案也不同，那么还要乘上 $deg_{rt}$。

> 具体来说就是考虑每个点第 $i$ 次经过他的时候的贡献是 $deg_u' - i + 1$，其中 $deg_u' = deg_u - 1$ 因为每个欧拉回路的最后一条边都可以作为内向生成树的一条边，$deg_u'$ 本质上就是除了内向生成树边以外的所有边。
>
> 对于根的情况我们特判因为其没有这样的边，也就是 $deg'_u = deg_u$，所以最后计算贡献的时候少算了一个 $deg_u$ 我们最后乘上即可。

------

### 例题

[Which Dreamed It](https://darkbzoj.tk/problem/3659)

> 就是板子。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
#define Getmod

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
const int mod  = 1000003; 
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
const int maxn = 1e2 + 5;
const int maxm = 2e5 + 5;

Tm fac[maxm];
Tm a[maxn][maxn];
int deg[maxn];
Tm ksm(Tm x,int mi) {
	Tm res(1);
	while(mi) {
		if(mi & 1) res *= x;
		mi >>= 1;
		x *= x;
	}
	return res;
}

void init(int x) {
	fac[0] = 1;
	for(int i = 1; i <= x; ++ i) fac[i] = fac[i - 1] * i;
}
int n;
Tm Guess() {
	Tm res(1), c(mod - 1);
	for(int i = 1; i <= n - 1; ++ i) {
		int pos(i);
		for(int j = i; j <= n - 1; ++ j) 
			if(a[j][i].z > 0) { pos = j; break; }
		if(pos != i) swap(a[pos], a[i]), res *= c;
		Tm inv = ksm(a[i][i], mod - 2);
		res *= a[i][i];
		for(int j = 1; j <= n - 1; ++ j) a[i][j] *= inv;
		for(int j = i + 1; j <= n - 1; ++ j) {
			for(int k = n - 1; k >= i; -- k) {
				a[j][k] -= a[i][k] * a[j][i];
			}
		}
	}
//	puts("Matrix");
//	for(int i = 1; i < n; ++ i) for(int j = 1; j < n; ++ j) printf("%d%c", a[i][j].z, " \n"[j == n - 1] );
//	puts("EndMatrix");
	return res;
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    init(2e5);
    while(scanf("%d", &n) != EOF) {
    	if(n == 0) break;
    	for(i = 1; i <= n; ++ i)
    	for(j = 1; j <= n; ++ j) 
    		a[i][j] = 0;
    	for(i = 1; i <= n; ++ i) {
    		int s; r1(s); deg[i] = s;
    		for(j = 1; j <= s; ++ j) {
    			int x; r1(x);
    			if(x != i) a[i][x] -= Tm(1), a[i][i].z ++;
    		}
    	}
    	
    	if(n == 1) { printf("%d\n", fac[deg[1]].z); continue; }
    	
    	Tm ans = Guess();
    	
    	for(i = 1; i <= n; ++ i) if(deg[i] > 0) ans *= fac[deg[i] - 1]; else ans = 0;
    	
    	ans *= deg[1];
    	
    	printf("%d\n", ans.z);
    	
    }
	
	return 0;
}
```

[AGC051D C4](https://atcoder.jp/contests/agc051/tasks/agc051_d)

这是一个无向图的欧拉回路计数，这个是 $\tt NPC$ 的，但是我们发现因为其满足欧拉回路的基本性质，也就是意味着如果钦定任意一条边比如 $u \to v$ 的个数，就可以推出所有边的限制。

之后直接进行 $\tt best$ 定理的计算了。

但是这里不同的就是，这里每条边是本质相同的，我们就不能直接使用排列，我们使用组合即可。

对于去除第一条边不能使用的情况，我们直接除以其方案数即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

//#define Fread
#define Getmod

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
const int mod  = 998244353;
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
const int maxn = 1e6 + 5;
const int maxm = 4;

Tm fac[maxn], inv[maxn], iv[maxn];

Tm ksm(Tm x,int mi) {
	Tm res(1);
	while(mi) {
		if(mi & 1) res *= x;
		mi >>= 1;
		x *= x;
	}
	return res;
}

void init(int x = 1e6) {
	fac[0] = 1;
	for(int i = 1; i <= x; ++ i) fac[i] = fac[i - 1] * i;
	inv[x] = ksm(fac[x], mod - 2);
	for(int i = x - 1; i >= 0; -- i) inv[i] = inv[i + 1] * (i + 1);
	iv[0] = 0;
	for(int i = 1; i <= x; ++ i) iv[i] = inv[i] * fac[i - 1];
}

int a, b, c, d;

Tm C(int a,int b) {
	return a < b ? 0 : fac[a] * inv[b] * inv[a - b];
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    init();
    Tm ans(0);
    r1(a, b, c, d);
    if((a & 1) == (b & 1) && (b & 1) == (c & 1) && (c & 1) == (d & 1)) {
    	for(int ia = 0; ia <= a; ++ ia) {
    		int ib = (b - a) / 2 + ia;
    		int ic = (c - b) / 2 + ib;
    		int id = (d - c) / 2 + ic;
    		if(ib < 0 || ic < 0 || id < 0 || ib > b || ic > c || id > d) continue;
//    		printf("%d %d %d %d\n", ia, ib, ic, id);
    		int A1 = 1ll * (ia + b - ib) * (ib + c - ic) % mod * (ic + d - id) % mod;
    		int C1 = 1ll * (ia + b - ib) * (ic) % mod * (ic - c) % mod;
    		int B1 = 1ll * (ib) * (ib - b) % mod * (ic + d - id) % mod;
    		Tm Del = ((A1 + B1 + C1) % mod + mod) % mod;
//    		printf("Del = %d\n", Del.z);
    	
    		Tm T1 = iv[ia + b - ib] * iv[ib + c - ic] * iv[ic + d - id];
    		Tm T2 = C(ia + b - ib, ia) * C(ib + c - ic, ib) * C(ic + d - id, ic) * C(id + a - ia, id);
    		ans += T1 * T2 * Del;
    	}
    }
    printf("%d\n", ans.z);
	return 0;
}
```


