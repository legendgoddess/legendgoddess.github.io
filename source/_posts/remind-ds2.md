---
title: 数据结构回忆2
date: 2022-3-21 21:05:20
tags:
  - 线段树
  - 平衡树
categories:
  - 专题
sticky: 1
---

[Legendgod's Blog](https://legendgod.ml/)

---

## 树状数组

一个函数 $\tt lowbit(x) = x \text{\&} (-x)$ 表示 $x$ 的最低二进制位的位置。

可以维护区间加区间和，常数比较**小**。

- $O(n)$ 建树，考虑对于节点 $x$ 向上更新 $x + \tt lowbit(x)$ 即可。

- 查询第 $K$ 小，就是经典二进制上二分，假设元素 $i$ 出现了 $a_i$ 次。

```cpp
int Kth(int k) {
    int cnt = 0, ret = 0;
    for(int i = log2(n); ~ i; -- i) {
        ret += (1 << i);
        if(ret >= n || cnt + t[ret] >= K) ret -= (1 << i);
        else cnt += ret;
    }
    return ret + 1;
}
```

- 时间戳优化，听起来听高级的，其实就是打标记看当前时刻有没有经过，经过的时候动态赋初值即可。

## 线段树

常常是可以维护满足如下信息的：

- 结合律，有 $a \otimes b \otimes c = a \otimes (b \otimes c)$。

- 封闭性，$\forall x, y \in S, x \otimes y \in S$。

- 存在单位元。

$\tt \text{广义线段树}：$

常规的线段树的 $\tt mid = \frac{l + r}{2}$，但是广义的线段树 $\tt mid$ 可以是任意的合法值。

### 线段树结构题

> 用于解决模拟线段树上的操作上的问题。

1. $[1, n]$ 的广义线段树 $[l, r]$ 内的区间定位数是 $2 \times (r - l+ 1) - |S|$，其中 $S$ 表示当前区间的子区间个数，区间定位数表示 $[l, r]$ 最少拆成多少线段树上区间的并。

取出所有在 $[l, r]$ 内的节点，显然构成了一个满二叉树森林，其中根的个数就是 $[l, r]$ 的区间定位数。

对于满二叉树其节点个数为 $2m -1$ 其中 $m$ 为叶子节点的个数。

所以总点数就是 $2(r - l + 1)  - ans = |S|$，其中 $ans$ 表示联通块的个数，那么 $\tt ans$ 就是根的数量，也就是区间定位数，可以得到 $ans = 2(r - l + 1) - |S|$。

2. $[1, n]$ 的线段树 $[l, r]$ 内的区间定位数是由 $[l - 1, l - 1], [r + 1, r + 1]$ 的 $\tt Lca$ (设其分别为 $L, R$) 到 $L$ 路径上的所有节点的右边一个的兄弟，和到 $R$ 路径上的所有节点的左边一个兄弟。

考虑设 $P$ 到所有祖先里面的左偏儿子的右兄弟集合为 $LFT(P)$ 右偏儿子的左兄弟的集合为 $RGT(P)$。

对于 $L, R$ 的 $\tt Lca$ 节点 $S$，其区间定位可以表示成如下的形式：

$$
(LFT(L) \setminus LFT(ls_S)) \cup (RGT(R) \setminus RGT(rs_S))
$$

3. 跳跃树，对于**广义线段树区间定位**更加强的结构。

> 这个东西之后再详细研究。[NOI 一轮复习 III：数据结构 - ix35_ 的博客 - 洛谷博客](https://www.luogu.com.cn/blog/ix-35/noi-yi-lun-fu-xi-iii-shuo-ju-jie-gou)

## 线段树技巧

1. 动态开点，可以考虑开内存池。

2. 线段树合并，复杂度是两棵树中节点较少的 $O(\text{节点数})$。

3. 线段树二分，信息具有二分性即可。

4. 线段树分裂，与线段树合并类似，可以使得权值线段树按照第 $\tt K$ 大进行分裂。

5. 线段树合并优化卷积，考虑 $\sum_i \sum_j val(i, j)$ 这样的式子，下标其实无所谓，但是重点是下标的上下界是明确的，比如说 $\sum_{i = 1} ^ n \sum_{j = i + 1} ^ n val(i ) \times val(j)$，这个东西可以看成对于一个 $j$ 使用左边的所有 $i$ 去更新它。

> 因为我比较菜，所以后面内容可能就是按照别人的博客写了，[线段树相关技巧的小小总结 - 木xx木大 的博客 - 洛谷博客](https://www.luogu.com.cn/blog/flyingfan/xian-duan-shu-xiang-guan-ji-qiao-di-xiao-xiao-zong-jie)

6. 线段树维护树直径：

可以考虑贪心，合并两个点集的时候，新的直径肯定是被合并的两个端点其中的一个。

> **不能有负权边**，这个常常会在一些题目，特别是**图**的离线算法并查集合并的时候出现。

考虑将点拍扁到**欧拉序**上，两点的距离就是 $dep_u + dep_v - 2\min_{i = u} ^ vdep_i$，这个就是维护区间 $\tt min, max$，还有 $lmin = dep_u - 2 \min_{i = l} ^ udep_i$，$rmin$ 同理。

合并的时候要么全部都在左边，要么全部在右边，要么跨越区间的端点。

```cpp
void pushup(int p) {
    t[p].mx = max(t[ls].mx, t[rs].mx), t[p].mn = min(t[ls].mn, t[rs].mn);
    t[p].lm = max((t[ls].lm, t[rs].lm), t[rs].mx - 2 * t[ls].mn);
    t[p].rm = max((t[ls].rm, t[rs].rm), t[ls].mx - 2 * t[rs].mn);
    t[p].ans = max(max(t[ls].ans, t[rs].ans), max(t[ls].mx + t[rs].lm, t[rs].mx + t[ls].rm));
}
```

7. 线段树维护单调栈

[楼房重建 - 洛谷](https://www.luogu.com.cn/problem/P4198)

这个东西可以转成斜率，本质上就是求这个斜率的前缀最大值的个数。

考虑左右端点分别维护前缀最大值，合并的时候：

- 左端点完全覆盖了右端点。

- 左端点没有完全覆盖右端点。

下面的情况是需要着重处理的，考虑我们需要找到一个合法的右端点的位置考虑进行线段树上二分可以发现对于每次单点修改的每个区间都要做一次，复杂度是 $O(n \log^2 n)$ 的。

> 不知道我之前是什么弱智，这种东西都要抄题解。

8. 线段树维护历史值

[CPU 监控 - 洛谷](https://www.luogu.com.cn/problem/P4314)

感觉总是比较弱智，多开几个标记就行了。

9. 线段树维护历史版本和

**问题**：维护一个数列 A，要求支持**区间加**，**区间查历史版本和**。

> 知道一下就行了，应该问题不大的。

[Good Subsegments - 洛谷](https://www.luogu.com.cn/problem/CF997E)

## 线段树维护动态 $\tt Dp$

将 $\tt Dp$ 的转移写成矩阵的形式即可，如果是树上的考虑维护轻重儿子的信息和圆方树有点类似。

[论动态 Dp | Legendgod's Blog](https://legendgod.ml/2021/09/26/ddp1/)

## 可持久化线段树维护并查集

> 之前不是很会，来学习一下。

这个东西本质上就是因为合并需要使用历史版本，对于每个节点记录该节点的父亲节点所在版本，复杂度是 $O(n \log^2 n)$ 的。

实现的时候注意按秩合并，主席树维护一下秩即可。

> 为啥我之前不会呀？我是弱智吧？

## 平衡树

没啥可讲的，会写 $\tt splay$ 和 $\tt fhq-treap$ 就行了。

注意区间翻转是可以使用平衡树处理的，其他的话线段树能走的平衡树也行，但是平衡树使用线段树合并的话是 $O(\log^2n)$ 的。

1. 区间平移

这个东西之前不是很会，感觉就是把这个区间 $[l, r]$ 拿出来之后给其他位置打上区间减法标记，然后连接到需要的位置合并上去就行了。

> 好弱智 /qd

## 动态树

> 只会 $\tt LCT$。

支持修改路径，实链剖分，换根，维护连通性等。

有些题目的复杂度可以通过实链剖分联想到使用 $\tt LCT$ 来维护。

[CF1344E Train Tracks 题解 | Legendgod's Blog](https://legendgod.ml/2022/04/01/cf-solution-1344e/)

1. 维护子树可增减信息

在 $\tt access$ 的时候减去原来信息，合并新的信息即可。

2. 维护子树不可增减信息

比如说维护最大值，使用 $\tt multiset$ 维护虚儿子的信息，在 `pushup` 和 `access` 修改即可。

> `Bjpers2:`  记得开始要把**自己的信息**加入堆中，以回避 `pushup` 时的特判。同类的还有最近/远白点。
> 
> ```cpp
> void access(int x){
>     for(int y=0;x;x=f[y=x]){
>         splay(x);
>         if(rs)   h[x].insert(mx[rs]);
>         if(rs == y) h[x].erase(h[x].find(mx[rs]));
>         pushup(x);
>     }
> }
> ```

3. 维护最小生成树

[[NOIP2013 提高组] 货车运输 - 洛谷](https://www.luogu.com.cn/problem/P1967)

> 这个题用到性质：最小生成树上的边权的最大值是最小的。所以直接 $\tt MST$ 之后树剖就行了。

[[NOI2014] 魔法森林 - 洛谷](https://www.luogu.com.cn/problem/P2387)

**简化题意：**

一条边的权值是 $(a, b)$ 对于一条路径的权值定义为 $\max(a) + \max(b)$ 求 $1 \to n$ 路径的最小权值。

直接想法就是考虑 $a$ 从小到大，之后维护最优的 $b$ 生成树计算答案，但是问题在于 $\tt splay$ 只能维护点权。我们考虑拆边 $(u, v, b)$ 变成 $(u, z), (z, v)$ 将点 $z$ 赋值上 $b$ 即可。

通过 $\tt link$ 和 $\tt cut$ 来处理，记录一下最大值的点编号即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
		#define Fread
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

using Read::r1;

const int maxn = 2e5 + 5;

int n, m;
struct Edd {
    int u, v, a, b;
    void init() { r1(u, v, a, b); }
    int operator < (const Edd &z) const {
        return a == z.a ? b < z.b : a < z.a;
    }
}E[maxn];


int f[maxn], sz[maxn];
int getfa(int x) { return x == f[x] ? x : f[x] = getfa(f[x]); }

void merge(int u,int v) {
    u = getfa(u), v = getfa(v);
    if(u == v) return ;
    if(sz[u] > sz[v]) swap(u, v);
    sz[v] += sz[u];
    f[u] = v;
}

int val[maxn], smx[maxn], ch[maxn][2], fa[maxn];
int rev[maxn];
#define ls ch[p][0]
#define rs ch[p][1]
int isrt(int x) { return ch[fa[x]][0] != x && ch[fa[x]][1] != x; }
int pd(int x) { return ch[fa[x]][1] == x; }
void pushup(int p) {
    smx[p] = p;
    if(ls && val[smx[ls]] > val[smx[p]]) smx[p] = smx[ls];
    if(rs && val[smx[rs]] > val[smx[p]]) smx[p] = smx[rs];
}
void Rotate(int p) {
    int f = fa[p], ff = fa[f], k = pd(p);
    if(ff && !isrt(f)) ch[ff][pd(f)] = p;
    fa[p] = ff, fa[f] = p;
    if(ch[p][!k]) fa[ch[p][!k]] = f;
    ch[f][k] = ch[p][!k];
    ch[p][!k] = f;
    pushup(f), pushup(p);
}

void Rev(int p) {
    if(!rev[p]) return ;
    swap(ls, rs);
    if(ls) rev[ls] ^= 1;
    if(rs) rev[rs] ^= 1;
    rev[p] = 0;
}

int buf[maxn];
void pushdown(int p) {
    int tot(1); buf[1] = p;
    for(int i = p; !isrt(i); i = fa[i]) buf[++ tot] = fa[i];
    for(int i = tot; i >= 1; -- i) Rev(buf[i]);
}

void Splay(int p) {
    pushdown(p);
    for(; !isrt(p); Rotate(p)) if(!isrt(fa[p])) {
        Rotate(pd(fa[p]) == pd(p) ? fa[p] : p);
    }
    pushup(p);
}

void access(int p) {
    for(int y = 0; p; y = p, p = fa[p]) {
        Splay(p), rs = y;
        if(y) fa[y] = p, pushup(p);
    }
}

int fdrt(int p) {
    access(p), Splay(p);
    for(; pushdown(p), ls; p = ls) ;
    Splay(p);
    return p;
}

void mkrt(int p) {
    access(p), Splay(p);
    rev[p] ^= 1;
}

void Link(int u,int v) { mkrt(u), fa[u] = v; }
void Cut(int u,int v) {
    mkrt(u), access(v), Splay(v);
    if(ch[v][0] != u || ch[u][0] != ch[u][1]) return ;
    ch[v][0] = 0, fa[u] = 0, pushup(v);
}

int getpos(int u,int v) {
    mkrt(u), access(v), Splay(v);
    return smx[v];
}

signed main() {
	int i, j;
    r1(n, m);
    for(i = 1; i <= m; ++ i) E[i].init();
    sort(E + 1, E + m + 1);
    for(i = 1; i <= n + m; ++ i) f[i] = i, sz[i] = 1;
    for(i = 1; i <= m; ++ i) val[i + n] = E[i].b;
    int ans(2e9);
    for(i = 1; i <= m; ++ i) {
        int u = E[i].u, v = E[i].v;
        int fl(1);
        if(getfa(u) == getfa(v)) {
            int w = getpos(u, v);
            if(val[w] > E[i].b)
                Cut(E[w - n].u, w), Cut(w, E[w - n].v);
            else fl = 0;
        }
        else merge(u, v);
        if(fl) Link(u, i + n), Link(i + n, v);
        if(getfa(1) == getfa(n)) ans = min(ans, E[i].a + val[getpos(1, n)]);
    }
    if(ans < 2e9) printf("%d\n", ans);
    else puts("-1");
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }


```






