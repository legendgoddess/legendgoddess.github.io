---
title: "CF1355F Guess Divisors Count 题解"
date: 2022-3-31 20:17:00
tags:
  - 随机化
categories:
  - CF题解
---

[Codeforces - 1355 F](https://codeforces.com/problemset/problem/1355/F)

> 这个题好神仙呀！等等好像也没有那么难 /qd

显然我们只要知道了 $\tt X$ 的质因子，游戏就结束了。

然后题目还是允许有误差的，所以我们其实甚至不用找到 $ > \sqrt X$ 的质因子了。

之后考虑怎样去试这些质因子，发现 $2^{30} > V$ 感觉不是很行。

但是题目有一个比较松的限制：$\frac{1}{2} \le \frac{ans}{d} \le 2$。这个东西意味着因子个数只要是答案的一半就合法了。

我们考虑这个 $7$ 是来干什么用的，发现我们最蠢也是输出 $1$，所以考虑什么的因子个数是 $8$。

- $3$ 个大质数相乘。

那么这个质数极限是多少？$\sqrt{10^9} = 10^3$。

考虑有更多质数相乘的情况，我们考虑肯定只有少量的大质数，根据条件我们是可以忽略一个大质数的，我们显然会忽略最大的那个。

具体来说我们考虑暴力枚举每个质数之后合并起来成为尽可能大的 $Q$，之后得到答案之后分解质因子得到哪些数是可以往大扩展的。

我们考虑对于如果一个数可能往大的方向扩展我们尽量优先扩展它，这个比漫无目的地找概率会相对大一点。

但是这个还是比较危，我们考虑能否舍弃一些准度来修改最终的答案。发现之前的 `三个大质数` 的情况我们的 $ans$ 是绰绰有余的，我们考虑枚举一下我们不能考虑的所有情况：

- 答案为 $1$。

- 一个大质数。

- 两个大质数相乘。

- 三个大质数相乘。

这些答案分别为 $1, 2, 3 \ \cup\ 4, 8$。我们可以直接对于 $ans \times 2$，也是完全没有问题的。

考虑极限情况：

1. $2$。

2. $4$。

3. $6, 8$。

4. $16$。

> 如果 $\times 3$ 或者更大就是不合法了。

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
typedef long long int64;
int n, m, pr[maxn], vis[maxn], tot(0);

void init(int x) {
    for(int i = 2; i <= x; ++ i) {
        if(!vis[i]) pr[++ tot] = i;
        for(int j = 1, k; k = 1ll * i * pr[j], k <= x && j <= tot; ++ j) {
            vis[k] = 1;
            if(!(i % pr[j])) continue;
        }
    }
}

struct Node {
    int64 a, vl, p;
    int operator < (const Node &z) const {
        return z.p == p ? vl > z.vl : p < z.p;
    }
};

Node Qr[maxn];
int qtot(0);

int64 gcd(int64 a,int64 b) { return b == 0 ? a : gcd(b, a % b); }
int64 Rans;

int64 getans(int64 x) {
    int64 res(0);
    for(int i = 1; i * i <= x; ++ i) if(!(x % i)) {
        ++ res;
        if(i * i != x) ++ res;
    }
    return res;
}

void Solve() {
    int i;
    static priority_queue<Node> q; while(!q.empty()) q.pop();
    for(int i = 1; i <= tot; ++ i) q.push({pr[i], pr[i], 1});
    int64 ans(1);
    for(int _ = 1; _ <= 22; ++ _) {
        if(q.empty()) break;
        int64 Q(1); qtot = 0;
        while(!q.empty()) {
            auto a = q.top();
            if((long double) Q > (long double)(1e18) / a.vl) break;
            Q *= a.vl;
            Qr[++ qtot] = a;
            q.pop();
        }
        cout << "? " << Q << endl;
        cout.flush();
        int64 x;
        cin >> x;
//        x = gcd(Q, Rans);
//        cout << "x = " << x << endl;

        for(i = 1; i <= qtot; ++ i) if(!(x % Qr[i].vl)) {
            ans /= Qr[i].p;
            Qr[i].p ++;
            ans *= Qr[i].p;
            Qr[i].vl *= Qr[i].a;
            if(Qr[i].vl > (int64)(1e9)) continue;
            q.push(Qr[i]);
        }
    }
    ans *= 2;
    cout << "! " << ans << endl;
    cout.flush();
//    auto X = getans(Rans);
//    if(abs(ans - Rans) <= 7 || 2 * ans <= X || ans <= 2 * X) puts("AC");
//    else puts("WA");
}

signed main() {
	int i, j, T(1);
	init(1000);
//    cin >> Rans;
    r1(T); while(T --) Solve();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```


