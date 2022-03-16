---
title: "][CTSC2015]日程管理 题解"
date: 2022-3-16 21:01:00
tags:
  - 数据结构
categories:
  - 洛谷题解
---

[P4511 [CTSC2015]日程管理 - 洛谷](https://www.luogu.com.cn/problem/P4511)

首先考虑对于第 $i$ 天，一开始在这之前肯定是可以放 $i$ 个任务的。

我们考虑当我们放了一个任务在天数 $t$ 的时候，$[t, T]$ 的可以放的任务数量肯定 $-1$。

我们考虑对于加入一个 $t$ 如果后面存在一个位置是不能放了，也就是可以放的任务数量是 $0$ 的情况，我们才需要考虑对于之前的位置进行取出判断。

显然这个东西是一个区间加的操作，我们考虑使用线段树进行维护。

考虑到我们每个点是可以撤销的，我们考虑对于加入和没有加入的集合分别进行维护即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Legendgod {
	namespace Read {
//		#define Fread
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

using namespace Read;

const int maxn = 3e5 + 5;
#define pr pair<int, int>
map<pr, int> mp;
int pre[maxn], nw[maxn];
int getid(const pr& z) {
    int x = mp[z], y = nw[x];
    nw[x] = pre[nw[x]];
    return y;
}

const int maxm = maxn << 2;
struct Segment1 {
    int tag[maxm], t[maxm], md[maxm];
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid md[p]
    void pushup(int p) { t[p] = min(t[ls], t[rs]); }
    void build(int p,int l,int r) {
        t[p] = l;
        if(l == r) return ;
        mid = (l + r) >> 1;
        build(ls, l, mid), build(rs, mid + 1, r);
    }
    void Add(int p,int c) {
        t[p] += c, tag[p] += c;
    }
    void pushdown(int p) {
        if(tag[p] == 0) return ;
        Add(ls, tag[p]), Add(rs, tag[p]);
        tag[p] = 0;
    }
    void Insert(int p,int l,int r,int ll,int rr,int c) {
        if(ll <= l && r <= rr) return Add(p, c);
        pushdown(p);
        if(ll <= mid) Insert(ls, l, mid, ll, rr, c);
        if(mid < rr) Insert(rs, mid + 1, r, ll, rr, c);
        pushup(p);
    }
    int Left(int p,int l,int r,int ll,int rr) {
        if(t[p]) return 0;
        if(l == r) return l;
        pushdown(p);
        int res(0);
        if(ll <= mid) res = Left(ls, l, mid, ll, rr);
        if(!res && mid < rr) res = Left(rs, mid + 1, r, ll, rr);
        pushup(p);
        return res;
    }
    int Right(int p,int l,int r,int ll,int rr) {
        if(t[p]) return 0;
        if(l == r) return l;
        pushdown(p);
        int res(0);
        if(mid < rr) res = Right(rs, mid + 1, r, ll, rr);
        if(!res && ll <= mid) res = Right(ls, l, mid, ll, rr);
        pushup(p);
        return res;
    }
    #undef ls
    #undef rs
    #undef mid
}Tm;

int ed;
struct Node {
    int t, p, us;
    Node(void) : us(0) {}
}qs[maxn];

struct Segment2 {
    set<pr> s[maxn];
    int tmx[maxm], tmn[maxm], ct[maxn];
    #define ls (p << 1)
    #define rs (p << 1 | 1)
    #define mid ((l + r) >> 1)
    int Max(int a,int b) {
        if(!a) return b;
        if(!b) return a;
        return qs[a].p > qs[b].p ? a : b;
    }
    int Min(int a,int b) {
        if(!a) return b;
        if(!b) return a;
        return qs[a].p > qs[b].p ? b : a;
    }
    void Add(int p,int l,int r,int pos,int num) {
        if(l == r) {
            ++ ct[l];
            s[l].insert({qs[num].p, num});
            tmn[p] = s[l].begin()->second;
            tmx[p] = s[l].rbegin()->second;
            return ;
        }
        if(pos <= mid) Add(ls, l, mid, pos, num);
        else Add(rs, mid + 1, r, pos, num);
        tmx[p] = Max(tmx[ls], tmx[rs]);
        tmn[p] = Min(tmn[ls], tmn[rs]);
    }
    void Del(int p,int l,int r,int pos,int num) {
//        printf("%d %d %d %d %d\n", p, l, r, pos, num);
        if(l == r) {
            -- ct[l];
            s[l].erase({qs[num].p, num});
            if(ct[l]) {
                tmn[p] = s[l].begin()->second;
                tmx[p] = s[l].rbegin()->second;
            }
            else tmx[p] = tmn[p] = 0;
            return ;
        }
        if(pos <= mid) Del(ls, l, mid, pos, num);
        else Del(rs, mid + 1, r, pos, num);
        tmx[p] = Max(tmx[ls], tmx[rs]);
        tmn[p] = Min(tmn[ls], tmn[rs]);
    }
    int Askmx(int p,int l,int r,int ll,int rr) {
//        if(ll > rr) return 0;
        if(ll <= l && r <= rr) return tmx[p];
        int res(0);
        if(ll <= mid) res = Max(res, Askmx(ls, l, mid, ll, rr));
        if(mid < rr) res = Max(res, Askmx(rs, mid + 1, r, ll, rr));
        return res;
    }
    int Askmn(int p,int l,int r,int ll,int rr) {
//        if(ll > rr) return 0;
        if(ll <= l && r <= rr) return tmn[p];
        int res(0);
        if(ll <= mid) res = Min(res, Askmn(ls, l, mid, ll, rr));
        if(mid < rr) res = Min(res, Askmn(rs, mid + 1, r, ll, rr));
        return res;
    }
    #undef ls
    #undef rs
    #undef mid
}Ti, To;

int n, m;
char s[10];
int tot(0);
long long ans(0);
int ts(0);

void Addsub(int x) {
    int tmp = Tm.Left(1, 1, n, qs[x].t, n);
//    puts("SSS");
    if(!tmp) {
        Tm.Insert(1, 1, n, qs[x].t, n, -1);
        Ti.Add(1, 1, n, qs[x].t, x);
//        puts("SSS");
        qs[x].us = 1;
        ans += qs[x].p;
    }
    else {
        int k = Ti.Askmn(1, 1, n, 1, tmp);
        if(qs[k].p < qs[x].p) {
//            printf("ts = %d\n", ts);
//            if(ts == 79) { printf("k = %d, ps = %d\n", k, qs[k].t); }
            ans += qs[x].p - qs[k].p;
            Tm.Insert(1, 1, n, qs[k].t, n, 1);
            Ti.Del(1, 1, n, qs[k].t, k);// Die
//            printf("ts1 = %d\n", ts);
            To.Add(1, 1, n, qs[k].t, k);
            Tm.Insert(1, 1, n, qs[x].t, n, - 1);
            Ti.Add(1, 1, n, qs[x].t, x);
            qs[k].us = 0, qs[x].us = 1;
        }
        else To.Add(1, 1, n, qs[x].t, x);
    }
}

void DelSub(int x) {
    if(qs[x].us == 0) To.Del(1, 1, n, qs[x].t, x);
    else {
        ans -= qs[x].p;
        Ti.Del(1, 1, n, qs[x].t, x);
        Tm.Insert(1, 1, n, qs[x].t, n, 1);
//        qs[x].us = 0;
        int k = Tm.Right(1, 1, n, 1, n), y = To.Askmx(1, 1, n, k + 1, n);
//        printf("(%d, %d)\n", k, y);
        if(y) {
            Tm.Insert(1, 1, n, qs[y].t, n, -1);
            To.Del(1, 1, n, qs[y].t, y);
            Ti.Add(1, 1, n, qs[y].t, y);
            ans += qs[y].p;
            qs[y].us = 1;
        }
    }
}

signed main() {// 79
//    freopen("1.in", "r", stdin);
//    freopen("11.ans", "w", stdout);
	int i, j;
    r1(n, m);
    Tm.build(1, 1, n);
    for(i = 1; i <= m; ++ i) {
        ++ ts;
        int t, p;
        scanf("%s", s + 1), r1(t, p);
        if(s[1] == 'A') {
            ++ ed;
            qs[ed].p = p, qs[ed].t = t;
            if(!mp[{t, p}]) mp[{t, p}] = ++ tot;
            int num = mp[{t, p}];
            pre[ed] = nw[num], nw[num] = ed;
            Addsub(ed);
        }
        else DelSub(getid({t, p}));
        printf("%lld\n", ans);
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```
