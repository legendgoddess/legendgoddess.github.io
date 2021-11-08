---
title: AC 自动机
date: 2021-11-08 15:52:00
tags:
  - 数据结构
  - Trie
  - 图论
  - 算法
categories: 
  - 数据结构
  - 算法
---

<h3><center>浅谈AC自动机</center></h3>

> 建议学过 AC 自动机的人来看。

注意我们一开始直接建立串的时候是 $\tt Trie$ 图，之后建立 $\tt fail$ 指针的时候才是真正的 $AC$ 自动机。

具体来说对于 $\tt Trie$ 上深度从小到大的一条链，对应的是一个曾经在某个或者多个串中出现过的子串。

如果说是一条从根到底的链那本质就是代表一个串，显然对于一条链之间的点，本质上深度小的是深度大的前缀串。

用处：

- 我们可以通过遍历 $\tt Trie$ 图来不重复地遍历每个字符串（不会有相交）。

---

还有一个东西叫 $\tt fail$ 树，这个东西的定义和 $\tt nxt$ 数组很类似，就是与当前串公共后缀最长的点。

那么我们一直沿着 $\tt fail$ 树跳的话是可以遍历到所有与当前串后缀部分有交的串，而且是交是从大到小的。

**注意**：

- 我们进行找 $\tt fail$ 指针的时候需要使用路径压缩，但是即使使用了路径压缩，我们直接建立 $\tt fail$ 树的时候完全不会有影响。

- 如果说拿节点 $1$ 当做超级源点的话，对于周围一圈的不存在的点，需要路径压缩至点 $1$。

用处：

- 发现对于一个节点 $x$，在 $\tt fail$ 树上的子树中的所有节点都是后缀包含其的节点。可以通过打标记，进行子树求和来得到该节点（字符串）出现的次数。

----

<h3><center>简单例题</center></h3>

> [[NOI2011] 阿狸的打字机](https://www.luogu.com.cn/problem/P2414)

容易看出题目给的输入，就是建立 $\tt Trie$ 的方式。我们建立 $\tt fail$ 树。

之后考虑 $(x, y)$ 我们可以通过枚举 $y$ 中的每一个点，判断其 $\tt fail$ 树上的祖先是否有 $x$ 的终止节点。

那么我们可以考虑遍历完所有的 $y$ 节点的时候，在 $\tt fail$ 树上的 $x$ 处，统计子树和。

发现因为我们遍历子串是通过 $\tt Trie$ 的，所以不同的字符串可以一起做，直接离线即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

namespace Legendgod {

namespace Read {
//    #define Fread
    #ifdef Fread
    const int Siz = (1 << 21) + 5;
    char buf[Siz], *iS, *iT;
    #define gc() (iS == iT ? (iT = (iS = buf) + fread(buf, 1, Siz, stdin), iT == iS ? EOF : *iS ++ ) : *iS ++)
    #define getchar gc
    #endif // Fread

    template <typename T>
    void r1(T &x) {
        x = 0; int f(1); char c(getchar());
        for(; !isdigit(c); c = getchar()) if(c == '-') f = -1;
        for(; isdigit(c); c = getchar()) x = (x * 10) + (c ^ 48);
        x *= f;
    }
    template <typename T, typename...Args>
    void r1(T &x, Args&...arg) {
        r1(x), r1(arg...);
    }

}

using namespace Read;

const int maxn = 2e5 + 5;
char s[maxn];
struct Node {
    int ch[26], fl, opt, fa;
    Node(void) {
        fl = opt = fa = 0;
        memset(ch, 0, sizeof(ch));
    }
}t[maxn];

int id[maxn];
vector<pair<int, int> > vc[maxn];
/*
pos, id
*/

int ans[maxn];

int tot(1);

struct Graph {
    int head[maxn], cnt;
    struct Edge {
        int to, next;
    }edg[maxn << 1];
    Graph(void) : cnt(0) {}
    void add(int u,int v) {
//        printf("Edge = %d %d\n", u, v);
        edg[++ cnt] = (Edge) {v, head[u]}, head[u] = cnt;
    }
}G;
constexpr int N = 26;
int ch[maxn][26];
void build() {
    for(int i = 1; i <= tot; ++ i) for(int j = 0; j < N; ++ j) ch[i][j] = t[i].ch[j];
    static queue<int> q; while(!q.empty()) q.pop();
    for(int i = 0; i < N; ++ i) if(t[1].ch[i]) q.push(t[1].ch[i]), t[t[1].ch[i]].fl = 1; else t[1].ch[i] = 1;
    while(!q.empty()) {
        int u = q.front(); q.pop();
        for(int i = 0; i < N; ++ i) if(t[u].ch[i]) {
            t[t[u].ch[i]].fl = t[t[u].fl].ch[i];
            q.push(t[u].ch[i]);
        }
        else t[u].ch[i] = t[t[u].fl].ch[i];
    }
    for(int i = 2; i <= tot; ++ i) G.add(t[i].fl, i);
}
struct Seg {
    int t[maxn << 1];
    int lowbit(int x) { return x & -x; }
    void add(int p,int c) { for(; p <= tot; p += lowbit(p)) t[p] += c; }
    int ask(int p) { int res(0); for(; p > 0; p -= lowbit(p)) res += t[p]; return res; }
    int ask(int l,int r) { return ask(r) - ask(l - 1); }
}T;
int dfin[maxn], dfot[maxn];
int dfntot;
void dfs1(int p,int pre) {
    dfin[p] = ++ dfntot;
    for(int i = G.head[p];i;i = G.edg[i].next) {
        int to = G.edg[i].to; if(to == pre) continue;
        dfs1(to, p);
    }
    dfot[p] = dfntot;
}
void dfs2(int p,int pre) {
    T.add(dfin[p], 1);
//    printf("p = %d\n", p);
    if(t[p].opt)
        for(auto v : vc[p]) ans[v.second] += T.ask(dfin[v.first], dfot[v.first]);
    for(int i = 0;i < N; ++ i) if(ch[p][i]) {
        dfs2(ch[p][i], p);
    }
    T.add(dfin[p], -1);
}

int m, n;

signed main() {
    int i, j;
    scanf("%s", s + 1);
    int Ln = strlen(s + 1);
    int now(1);
    for(i = 1; i <= Ln; ++ i) {
        if('a' <= s[i] && s[i] <= 'z') {
//            puts("SSS");
            if(!t[now].ch[s[i] - 'a'])
                t[now].ch[s[i] - 'a'] = ++ tot;
            int tmp = now;
            now = t[now].ch[s[i] - 'a'];
            t[now].fa = tmp;
        }
        else if(s[i] == 'P') t[now].opt = 1, id[++ n] = now;
        else now = t[now].fa;
    }
    r1(m);
    for(i = 1; i <= m; ++ i) {
        int x, y;
        r1(x, y);
        vc[id[y]].push_back(make_pair(id[x], i));
    }
    build();
    dfs1(1, 0);
//    for(i = 1; i <= tot; ++ i) printf("%d : %d %d\n", i, dfin[i], dfot[i]);
    dfs2(1, 0);
    for(i = 1; i <= m; ++ i) printf("%d\n", ans[i]);
    return 0;
}

}

signed main() { return Legendgod::main(), 0; }

```

