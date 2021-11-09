---
title: CF1305G Kuroni and Antihype
date: 2021-08-17 06:57:43
tags:
  - 图论
  - 最小生成树
categories:
  - CF题解
---

### [CF1305G](https://codeforces.com/problemset/problem/1305/G)

> 可以算说我自己做出来的 $3500$ 题吧。虽然这是很多年前的题。

这个东西我们可以尝试一下建图，也就是如果两个点是朋友关系我们可以使其连边。

然后会发现有很多的联通块，那么我们不妨对于一个联通块来考虑。

我们贪心得想肯定要让小的邀请大的点。然后邀请的情况肯定构成一个树，也就是邀请的边数是点数 $-1$。

> 因为不能重复邀请。

我们考虑这个树的情况需要使用，会想到最大生成树。然后要如何设置边权？可以发现对于一条边本质上两边都可以使用那么边权就是 $a_u + a_v$。但是每一个点肯定需要一次被加入，那么就需要减去所有点的点权。

但是我们第一个选择的点不知道是谁，总不可能暴力枚举吧，发现可以建立一个 $0$ 节点，链接所有的节点，这样第一个点的贡献也可以计算了。

现在可以保证整张图是联通的，我们计算最小生成树即可。

最小生成树总共有 $3$ 种求法。$prim$ 和 $kruscal$ 都需要预先知道全部的边。那么我们可以使用 $Boruvka$。在计算的过程中我们只需要进行高维前缀和即可。

因为最大的边权可能被计算过了，那么我们维护一个和最大边权所属联通块不同的次大边权即可。

> 第一次写 $Boruvka$ 感觉细节特别多，最后还是重构一遍过的。
---

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
const int maxn = 2e5 + 5;

int n;
long long ans;
int fa[maxn], siz[maxn], v[maxn];

int getfa(int x) {
    return x == fa[x] ? x : fa[x] = getfa(fa[x]);
}

bool merge(int u,int v) {
    int s1 = getfa(u),  s2 = getfa(v);
    if(s1 == s2) return false;
    if(siz[s1] > siz[s2]) swap(s1, s2);
    fa[s1] = s2, siz[s2] += siz[s1];
    return true;
}

const int maxm = (1 << 18) + 5;

const int N = 18, z = (1 << 18);

#define pii pair<int, int>

#define id second
#define val first

pii sum[maxm][2], mx[maxn];

void update(pii a[2], pii b[2]) {
    for(int i = 0; i < 2; ++ i) {
        pii t = b[i];
        if(a[0].val < t.val || (a[0].val == t.val && a[0].id != t.id)) {
            if(a[0].id != t.id) a[1] = a[0];
            a[0] = t;
        }
        else if(a[1].val < t.val && (t.id != a[0].id)) a[1] = t;
    }
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i, j;
    r1(n);
    for(i = 1; i <= n + 1; ++ i) fa[i] = i, siz[i] = 1;
    ans = 0;
    for(i = 1; i <= n; ++ i) r1(v[i]), ans -= v[i];
    int ct(n);
    while(ct > 0) {
        for(i = 0; i < z; ++ i) sum[i][0] = sum[i][1] = make_pair(-1, -1);
        for(i = 1; i <= n + 1; ++ i) {
            int s1 = getfa(i), w = v[i];
            if(sum[w][0].id == -1) sum[w][0] = make_pair(w, s1);
            else if(sum[w][1].id == -1 && sum[w][0].id != s1) sum[w][1] = make_pair(w, s1);
        }

        for(i = 0; i < N; ++ i) {
            for(int S = 0; S < z; ++ S) if((S >> i) & 1) {
                update(sum[S], sum[S ^ (1 << i)]);
            }
        }

        for(i = 1; i <= n + 1; ++ i) mx[i] = make_pair(-1, -1);

        for(i = 1; i <= n + 1; ++ i) {
            int nv = (z - 1) ^ v[i], s1 = getfa(i);
            if(sum[nv][0].id == -1) continue;
            if(sum[nv][0].val + v[i] > mx[s1].val && sum[nv][0].id != s1) mx[s1] = sum[nv][0], mx[s1].val += v[i];
            if(sum[nv][1].val + v[i] > mx[s1].val && sum[nv][1].id != s1) mx[s1] = sum[nv][1], mx[s1].val += v[i];
        }

        for(i = 1; i <= n + 1; ++ i) if(getfa(i) == i && mx[i].id != -1 && merge(mx[i].id, i)) {
            -- ct, ans += mx[i].val;
        }

    }

    printf("%lld\n", ans);

	return 0;
}
```




