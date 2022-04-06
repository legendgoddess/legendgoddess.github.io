---
title: "CF1290D Coffee Varieties (hard version) 题解"
date: 2022-4-6 13:37:00
tags:
  - 交互
categories:
  - CF题解
---

[Problem - 1290D - Codeforces](https://codeforces.com/problemset/problem/1290/D)

### $\text{my solution}$

不妨考虑初始答案为 $n$，发现一个数是出现过的直接让答案 $-1$。

我们考虑如何查看一个数是出现过的。

我们先尝试能否让一个数和其他所有数进行匹配，考虑一个数消耗的次数是 $\frac{n- 1}{k -1}$。为了钦定顺序我们考虑位置 $i$ 只和位置在其前面的数进行匹配。

发现直接暴力匹配的话如果 $K$ 比较大会浪费掉很多查询次数，所以考虑如何利用 $K$。

如果考虑钦定了顺序，对于 $i \le K$ 的部分我们可以一次全部查完。

其次我们发现重置队列对于查询次数的影响还是很大的，能不能尽量少重置队列。

- 只要一个数没有出问题，其查询队列中可以包含符合范围的任意数。

- 如果后面的数和当前的数是不同的，查询队列是允许出现后面的数的。

注意每次加入数的时候可能会弹出数。

看看能不能倒着查询，就是让每个数被查询，考虑需要查询同一个块内的所有数，我们加入块之后。逐步加入原来的数。我们考虑同一块内的数倒着加入，然后依次查询前面的一个块。

我们只要保证块内的数互不相同，而且加入的数互不相同即可。

> 于是我们处理完了两个块的情况。

考虑更多块要怎么办，考虑先处理完相邻两个块，之后再处理前面的块，发现复杂度是两个块的大小的两倍，因为要加入查询两次。

每个块需要贡献其前面的块数 $\times 2 + 1$ 次。

所以查询次数是，他还说了肯定是正好分成整块的，因为不是整块直接就做完了：

$$
k \sum_{i = 1} ^ {\frac{n}{k}} 4(i - 1) - k\sum_{i = 1} ^ {\frac{n}{k}} 2 = \frac{2n^2}{k} - 4n
$$

> 可能会被卡。

发现还能优化，就是对于同一块我们不要清空继续向后来查询。

考虑之前我们算两个块的时候是怎么做的，就是需要查询块 $A, B$，事实上我们将 $B$ 所有元素从左向右压入，之后压入 $A$，进行依次查询。

> 如果 $B$ 中有重复元素其实可以忽略，如果 $A$ 中有重复元素可以开桶记录表示当前位置在 $A$ 中是否有重复，如果有的话压入就不用管返回值。
> 
> 事实上重复其实可以不考虑。 /qd

之后发现恰好 $A$ 也全部按照顺序压入，所以可以向左边的一个块进行查询。

我们考虑查询块 $i, i - 2$ 的情况，我们会查询到 $i - 4, i - 2$。本质上就是按照余数分组可以进行查询，发现我们不需要考虑重复压入的情况，查询完跳跃 $x$ 就进行了 $n$ 次操作，所以操作的复杂度是 $O(\frac{n^2}{k})$ 的。

---

> 这个我肯定是对的。

### $\text{solution}$

有更好实现的方法。

考虑将每个块看成点，就是一张完全图，那么原问题就是对其进行链剖分，对于每个起点使得链的数量尽量少。

枚举每个起点 $s$ 走出一条不重复路径 $s \to s - 1 \to s + 1 \to s - 2 \to s + 2 \to \cdots$。

发现对于每个起点 $s$ 所有边恰好经过一次，复杂度是 $O(\frac{n^2}{k})$ 的。

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
int n, K;

void Clear() { cout << "R" << endl; }
int In(int x) { cout << "? " << x << endl; char y; cin >> y; return y == 'Y'; }

int Bx, a[maxn];
int St(int x) { return (x - 1) * K + 1; }
int Ed(int x) { return x * K; }

void Query(int id) {
    for(int i = St(id); i <= Ed(id); ++ i) if(a[i]) {
        if(In(i)) a[i] = 0;
    }
}

signed main() {
	int i, j;
    r1(n, K);
    Bx = n / K;
    for(i = 1; i <= n; ++ i) a[i] = 1;
    for(int st = 1; st <= Bx; ++ st) {
        Clear();
        for(int d = 0, i = 1; i <= Bx; ++ i) {
            int x = st + d;
            while(x <= 0) x += Bx;
            while(x > Bx) x -= Bx;
            Query(x);
            if(d >= 0) d = - d - 1;
            else d = -d;
        }
    }
    int ans(0);
    for(i = 1; i <= n; ++ i) ans += a[i];
    cout << "! " << ans << endl;
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//

```


