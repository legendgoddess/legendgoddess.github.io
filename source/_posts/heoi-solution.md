---
title: "「HEOI2015」最短不公共子串"
date: 2021-08-26 09:22:24
tags:
  - 数据结构
  - Dp
categories:
  - 省选题解
---

### [「HEOI2015」最短不公共子串](https://loj.ac/p/2123)

**题目大意：**

给两个小写字母串 $A, B$ 请你计算：

1. $A$ 的一个最短的子串，它不是 $B$ 的子串。
2. $A$ 的一个最短的子串，它不是 $B$ 的子序列。
3. $A$ 的一个最短的子序列，它不是 $B$ 的子串。
4. $A$ 的一个最短的子序列，它不是 $B$ 的子序列。

---

简单题。看了一下网上的题解有用 $dp$ 的。但是既然是最小长度可以考虑使用广度优先搜索。

首先这个可以保证找到的第一个就是最优解。其次因为我们两个自动机都是按照深度向下排序的，那么本质上就是先遍历完深度浅的再遍历深度深的。

我们直接对于每个串建立两个自动机，之后暴力跑即可。

因为作者的写法习惯，所以后缀自动机的超级原点是 $1$ 号节点。为了判断的方便，我们考虑让后缀自动机的 $f(i, c)$ 表示从 $i + 1$ 开始的第一个为 $c$ 的位置。

这样超级原点也是 $1$，方便处理。

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

//#define int long long
const int maxn = 2e3 + 50;

char A[maxn], B[maxn];

int vis[maxn << 1][maxn << 1];

struct Point {
    int fa, ch[26], len;
    Point(void) {fa = len = 0; memset(ch, 0, sizeof(ch));}
};

struct SAM {
    int tot, las;
    Point d[maxn << 1];
    SAM(void) : tot(1), las(1) {}
    void Insert(int c) {
        int p = las;
//        printf("p = %d\n", p);
        int np = las = ++ tot;
        d[np].len = d[p].len + 1;
        for(; p && !d[p].ch[c]; p = d[p].fa) d[p].ch[c] = np;
        if(!p) d[np].fa = 1;
        else {
            int q = d[p].ch[c];
            if(d[q].len == d[p].len + 1) d[np].fa = q;
            else {
                int nq = ++ tot; d[nq] = d[q];
                d[nq].len = d[p].len + 1;
                d[q].fa = d[np].fa = nq;
                for(; p && (d[p].ch[c] == q); p = d[p].fa) d[p].ch[c] = nq;
            }
        }
    }
}SA, SB;

struct Sufix {
    int f[maxn][26], las[26];
    void Insert(char *A, int n) { // 从第 i + 1 个位置开始，字符 c 出现的第一个位置，为了方便将位置
        for(int i = n; i >= 0; -- i) { //向后移动一位
            for(int j = 0; j < 26; ++ j) if(las[j]) {
                f[i + 1][j] = las[j];
            }
            las[A[i] - 'a'] = i + 1;
        }
    }
}FA, FB;


struct Node {
    int dep, ida, idb;
    Node(int a = 0, int b = 0,int c = 0) : dep(a), ida(b), idb(c) {}
};

queue<Node> q;
void Clear() {
    memset(vis, 0, sizeof(vis));
    while(!q.empty()) q.pop();
}


int bfs1() {
    Clear();
    vis[1][1] = 1;
    q.push(Node(0, 1, 1));
    while(!q.empty()) {
        Node u = q.front(); q.pop();
        int pa = u.ida, pb = u.idb;
        for(int i = 0; i < 26; ++ i) {
            if(vis[SA.d[pa].ch[i]][SB.d[pb].ch[i]]) continue;
            vis[SA.d[pa].ch[i]][SB.d[pb].ch[i]] = 1;
//            if(u.dep == 0) printf("d0 : pa = %d, pb = %d\n", SA.d[pa].ch[i], SB.d[pb].ch[i]);
            if(SA.d[pa].ch[i] && !SB.d[pb].ch[i]) return u.dep + 1;
            if(SA.d[pa].ch[i]) {
                q.push(Node(u.dep + 1, SA.d[pa].ch[i], SB.d[pb].ch[i]));
            }
        }
    }
    return -1;
}

int bfs2() {
    Clear();
    vis[1][1] = 1;
    q.push(Node(0, 1, 1));
    while(!q.empty()) {
        Node u = q.front(); q.pop();
        int pa = u.ida, pb = u.idb;
        for(int i = 0; i < 26; ++ i) {
            if(vis[SA.d[pa].ch[i]][FB.f[pb][i]]) continue;
            vis[SA.d[pa].ch[i]][FB.f[pb][i]] = 1;
            if(SA.d[pa].ch[i] && !FB.f[pb][i]) return u.dep + 1;
            if(SA.d[pa].ch[i]) {
                q.push(Node(u.dep + 1, SA.d[pa].ch[i], FB.f[pb][i]));
            }
        }
    }
    return -1;
}

int bfs3() {
    Clear();
    vis[1][1] = 1;
    q.push(Node(0, 1, 1));
    while(!q.empty()) {
        Node u = q.front(); q.pop();
        int pa = u.ida, pb = u.idb;
        for(int i = 0; i < 26; ++ i) {
            if(vis[FA.f[pa][i]][SB.d[pb].ch[i]]) continue;
            vis[FA.f[pa][i]][SB.d[pb].ch[i]] = 1;
            if(FA.f[pa][i] && !SB.d[pb].ch[i]) return u.dep + 1;
            if(FA.f[pa][i]) {
                q.push(Node(u.dep + 1, FA.f[pa][i], SB.d[pb].ch[i]));
            }
        }
    }
    return -1;
}

int bfs4() {
    Clear();
    vis[1][1] = 1;
    q.push(Node(0, 1, 1));
    while(!q.empty()) {
        Node u = q.front(); q.pop();
        int pa = u.ida, pb = u.idb;
        for(int i = 0; i < 26; ++ i) {
            if(vis[FA.f[pa][i]][FB.f[pb][i]]) continue;
            vis[FA.f[pa][i]][FB.f[pb][i]] = 1;
            if(FA.f[pa][i] && !FB.f[pb][i]) return u.dep + 1;
            if(FA.f[pa][i]) {
                q.push(Node(u.dep + 1, FA.f[pa][i], FB.f[pb][i]));
            }
        }
    }
    return -1;
}


signed main() {
//    freopen("sus10.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int la, lb, i, j;
    scanf("%s%s", A + 1, B + 1);
    la = strlen(A + 1), lb = strlen(B + 1);
    for(i = 1; i <= la; ++ i) SA.Insert(A[i] - 'a');
    for(i = 1; i <= lb; ++ i) SB.Insert(B[i] - 'a');

    FA.Insert(A, la), FB.Insert(B, lb);
//    printf("e = %d\n", SA.d[1].ch['e' - 'a']);
    printf("%d\n%d\n%d\n%d\n", bfs1(), bfs2(), bfs3(), bfs4());

	return 0;
}
```




