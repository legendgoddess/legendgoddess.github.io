---
title: "CF1310E Strange Function 题解"
date: 2022-4-6 08:21:00
tags:
  - Dp
categories:
  - CF题解
---

[Problem - 1310E - Codeforces](https://codeforces.com/problemset/problem/1310/E)

考虑倒推，每个数具体是啥是不知道的，但是我们可以知道数的种类。

本质上对于集合中的每一个元素，就代表一种不同的元素。

如果进行一次逆变换，元素种类就会变成原来集合 $\sum a_i$。

一开始是有限定的，集合大小小于等于 $n$。

先处理 $k = 1$ 的情况：划分数 $\tt Dp$。

$k = 2$ 的情况，就是考虑需要倒推两次，不妨考虑当前集合是 $c$，考虑 $b$ 中出现次数为 $c_i$ 的数是 $v_i$，可以得到 $\sum c_i v_i \le n$。

我们只需要考虑其存在性即可，我们不妨让 $c_i$ 递减，之后 $v_i = i$。这样可以取到最小值。

所以我们只需要满足 $\sum c_ii \le n$ 即可，考虑进行 $\tt Dp$ 因为钦定了 $c_i$ 的关系，我们需要二维的状态复杂度 $O(n ^ 3)$，但是事实上我们钦定 $c_i$ 所以只需要枚举差分值就行了。考虑再推式子。

我们让 $t_i = c_i - c_{i + 1}$。

$$
\begin{aligned}
\sum_i i \sum_{j = i} ^ m t_j &= \sum_{j = 1} ^ m t_j \sum_{i = 1} ^ j i \\\\
&= \sum_{j = 1} ^ m t_j \times \frac{(j + 1) j} {2}
 \end{aligned}
$$

可以发现每个 $t_j$ 的贡献是 $\frac{(j + 1)j}{2}$。直接进行 $\tt Dp$ 完全背包。

考虑 $k =3$ 的情况：根据我们之前说过这个对于 $k$ 稍微大一点的话，函数的方案数会明显减少，我们不妨来证明一下。

考虑 $k = 3$ 的时候对于已经确定的权值 $m$ 怎么取到最小的 $n$，显然是填 $m$ 个 $1$ 最优。

那么对于上面一层就是 $\{1,2,3,\cdots,m-1,m\}$。权值是 $\frac{(m + 1)m}{2} \le n$。

显然 $\max_m = 64$，发现 $64$ 的拆分数是可以接受的，可以暴力枚举这个东西然后判断合法。

如果 $k \ge 4$ 会发现 $\max_m = 23$ 左右，这个可以直接进行剪枝保证复杂度。

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

const int maxn = 2020 + 5;
const int mod = 998244353;
int n, K;

template <typename T>
void Add(int& x,const T& c) { x = (x + c) % mod; }

namespace K1 {
    int f[maxn], ans(0);
    signed main() {
        int i, j;
        f[0] = 1;
        for(i = 1; i <= n; ++ i) for(j = i; j <= n; ++ j)
            Add(f[j], f[j - i]);
        for(i = 1; i <= n; ++ i) Add(ans, f[i]);
        printf("%d\n", ans);
        return 0;
    }
}

namespace K2 {
    int f[maxn], ans(0);
    signed main() {
        int i, j;
        f[0] = 1;
        for(i = 1; i * (i + 1) / 2 <= n; ++ i) {
            int c = (i + 1) * i / 2;
            for(j = c; j <= n; ++ j)
                Add(f[j], f[j - c]);
        }
        for(i = 1; i <= n; ++ i) Add(ans, f[i]);
        printf("%d\n", ans);
        return 0;
    }
}

namespace K3 {
    vector<int> vc;
    int ans(0);

    int check() {
        vector<int> tmp = vc, tmp1; tmp1.clear();
        int i, j;
        for(i = K; i > 1; -- i) {
            tmp1 = tmp, tmp.clear();
            sort(tmp1.begin(), tmp1.end()), reverse(tmp1.begin(), tmp1.end());
            int sum(0);
            for(j = 0; j < tmp1.size(); ++ j) sum += tmp1[j] * (j + 1);
            if(sum > n) return false;
            if(i > 4 && sum > 23) return false;
            for(j = 0; j < tmp1.size(); ++ j) {
                while(tmp1[j] --) {
                    tmp.push_back(j + 1);
                }
            }
        }
        return true;
    }

    int dfs(int lim) {
        if(!check()) return false;
        Add(ans, 1);
        for(int i = lim, fl; ; ++ i) {
            vc.push_back(i), fl = dfs(i), vc.pop_back();
            if(!fl) return true;
        }
        return true;
    }

    signed main() {
        dfs(1), void();
        printf("%d\n", ans == 0 ? mod - 1 : ans - 1);
        return 0;
    }

}

signed main() {
	int i, j;
    r1(n, K);
    if(K == 1) return K1::main();
    else if(K == 2) return K2::main();
    else return K3::main();
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//


```










