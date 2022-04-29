---
title: "AT2369 [AGC013C] Ants on a Circle 多解法讨论"
date: 2022-4-29 16:01:00
tags:
  - 贪心
categories:
  - AT题解
---

#### [AT2369 [AGC013C] Ants on a Circle](https://www.luogu.com.cn/problem/AT2369)

> 应该是第二次做到这个题了，但是还是没有写出来。第一次没有看懂题意不应该是理由，倒数的几篇题解，省选退役了。。。

首先如果不考虑编号，我们穿过和返回本质是一样的。

其次对于碰撞我们可以看成穿过之后编号进行了交换，那么我们就需要计算交换编号之后的情况。

- 节点的相对顺序是不变的。
- 节点经过 $m$ 时间之后绝对位置是不变的。

我们考虑计算每个点的编号，这样就可以不用考虑返回的情况。

---

### $\tt Solution \ 1$

考虑对于一个点，考虑与其相反的点和其碰撞的次数。

我们可以考虑到这个点和相反的所有点碰撞，可以看成两部分，一部分是 $k$ 倍的 $m$ 时间，还有剩余的时间。

直接使用二分即可。

考虑从位置 $x$ 走可以分成 $x + T$ 和 $x$ 部分进行计算贡献，这样计算周期就是经过 $0$ 的了，之后考虑 $v \equiv p \pmod m$，可以直接计算出周期和单独部分。

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
#define int long long
const int maxn = 2e5 + 5;
int n, m, T;
int l[maxn], sl, r[maxn], sr;
struct Node {
    int op, d;
}a[maxn];
int x[maxn], ans[maxn], id[maxn];

int Q2(int x) {
    int tmp = (x % m + m) % m, tmp1 = (x - tmp) / m;
    return upper_bound(r, r + sr, tmp) - r + tmp1 * sr;
}

int Q1(int x) {
    int tmp = (x % m + m) % m, tmp1 = (x - tmp) / m;
    return upper_bound(l, l + sl, tmp) - l + tmp1 * sl;
}

signed main() {
	int i, j;
    r1(n, m, T);
    for(i = 0; i < n; ++ i) {
        int op, x;
        r1(x, op);
        a[i] = {op, x};
        if(op == 1) r[sr ++] = x;
        else l[sl ++] = x;
    }
    for(i = 0; i < n; ++ i) {
        x[i] = a[i].d;
        if(a[i].op == 1) {
            x[i] = (x[i] + T) % m;
            id[i] = (i + Q1(a[i].d + 2 * T) - Q1(a[i].d)) % n;
        }
        else {
            x[i] = ((x[i] - T) % m + m) % m;
            id[i] = (i - Q2(a[i].d - 1) + Q2(a[i].d - 2 * T - 1)) % n;
            id[i] = (id[i] + n) % n;
        }
//        printf("%d : %d\n", i, id[i]);
        ans[id[i]] = x[i];
    }
    for(i = 0; i < n; ++ i) printf("%lld\n", ans[i]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```

---

### $\tt Solution\ 2$

有一说一思路是一样的，有更好的实现方法。

可以先求出最终的的所有位置，之后求出平移了多少，之后平移回来。

我们考虑从 $0$ 从顺时针开始走给每个点钦定，那么如果有一个点逆时针走过了 $0$ 那么最终位置会向前移动一位，不然就是向后。

本质就是计算走了几圈。

> 注意负数需要取下整的操作。

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

const int maxn = 2e5 + 5;
int n, m, T, ans[maxn];

signed main() {
	int i, j, del(0);
    r1(n, m, T);
    for(i = 0; i < n; ++ i) {
        int x, op; r1(x, op);
        x += (op == 1 ? T : -T);
        del += (int)floor(1.0 * x / m);
        ans[i] = ((x % m) + m) % m;
    }
    del = (del % n + n) % n;
    sort(ans, ans + n);
    for(i = 0; i < n; ++ i) printf("%d\n", ans[(i + del) % n]);
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```



