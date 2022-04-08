---
title: "pbds 学习笔记"
date: 2022-4-7 21:13:00
tags:
  - 堆
  - 线段树
  - 平衡树
  - Hash
categories:
  - 算法
---

主要是 $\tt pbds$ 有很多封装好了的小常数代码，比自己手写会方便很多。

头文件 `#include<bits/extc++.h>`，需要使用命名空间 `__gnu_pbds`

### 平衡树

[【模板】普通平衡树 - 洛谷](https://www.luogu.com.cn/problem/P3369)

$\tt STL$ 容器：`tree`

```cpp
tree<Key, Mapped, Cmp_Fn = std::less<Key>, Tag = rb_tree_tag,
                  Node_Update = null_tree_node_update,
                  Allocator = std::allocator<char> >
```

- `Key`: 储存的元素类型，如果想要存储多个相同的 `Key` 元素，则需要使用类似于 `std::pair` 和 `struct` 的方法，并配合使用 `lower_bound` 和 `upper_bound` 成员函数进行查找
- `Mapped`: 映射规则（$\tt Mapped-Policy$）类型，如果要指示关联容器是 **集合**，类似于存储元素在 `std::set` 中，此处填入 `null_type`，低版本 `g++` 此处为 `null_mapped_type`；如果要指示关联容器是 **带值的集合**，类似于存储元素在 `std::map` 中，此处填入类似于 `std::map<Key, Value>` 的 `Value` 类型
- `Cmp_Fn`: 关键字比较函子，例如 `std::less<Key>`
- `Tag`: 选择使用何种底层数据结构类型，默认是 `rb_tree_tag`。`__gnu_pbds` 提供不同的三种平衡树，分别是：
  - `rb_tree_tag`：红黑树，一般使用这个，后两者的性能一般不如红黑树
  - `splay_tree_tag`：$splay$ 树。
  - `ov_tree_tag`：有序向量树，只是一个由 `vector` 实现的有序结构，类似于排序的 `vector` 来实现平衡树，性能取决于数据想不想卡你
- `Node_Update`：用于更新节点的策略，默认使用 `null_node_update`，若要使用 `order_of_key` 和 `find_by_order` 方法，需要使用 `tree_order_statistics_node_update`
- `Allocator`：空间分配器类型

#### 成员函数

- `insert(x)`：向树中插入一个元素 $x$，返回 `std::pair<point_iterator, bool>`。
- `erase(x)`：从树中删除一个元素/迭代器 $x$，返回一个 `bool` 表明是否删除成功。
- `order_of_key(x)`：返回 $x$ 以 `Cmp_Fn` 比较的排名，排名为 $< x$ 的数的个数。
- `find_by_order(x)`：返回 `Cmp_Fn` 比较的排名所对应元素的迭代器，排名区间为 $[0, siz-1]$。
- `lower_bound(x)`：以 `Cmp_Fn` 比较做 `lower_bound`，返回迭代器。
- `upper_bound(x)`：以 `Cmp_Fn` 比较做 `upper_bound`，返回迭代器。
- `join(x)`：将 $x$ 树并入当前树，前提是两棵树的类型一样，$x$ 树被删除。
- `split(x,b)`：以 `Cmp_Fn` 比较，小于等于 $x$ 的属于当前树，其余的属于 $b$ 树。
- `empty()`：返回是否为空。
- `size()`：返回大小。

---

首先 `pbds` 是不支持区间翻转的，所以这个比 $\tt set$ 优秀的一点仅仅是在于查询排名和第 $K$ 大，还有平衡树的分裂合并。

> 合并是按照值进行分裂的，而不是按照大小分裂的，不过这个也无关紧要。

对于平衡树是**去重的**。

[#104. 普通平衡树](https://loj.ac/s/1436294)

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_cxx;
using namespace __gnu_pbds;

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
int n, m;
typedef long long int64;
tree<int64, null_type, less<int64>, rb_tree_tag, tree_order_statistics_node_update> rb;

signed main() {
	int i, j;
    r1(n);
    for(i = 1; i <= n; ++ i) {
        int64 opt, x, ans;
        r1(opt, x), x <<= 20;
        switch(opt) {
            case 1: rb.insert(x + i); break;
            case 2: rb.erase(rb.lower_bound(x)); break;
            case 3: printf("%lld\n", 1 + rb.order_of_key(x)); break;
            case 4: ans = *rb.find_by_order((x >> 20) - 1), printf("%lld\n", ans >> 20); break;
            case 5: ans = *-- rb.lower_bound(x), printf("%lld\n", ans >> 20); break;
            case 6: ans = *rb.upper_bound(x + n), printf("%lld\n", ans >> 20); break;
        }
    }
	return 0;
}
/*
10
1 106465
4 1
1 317721
1 460929
1 644985
1 84185
1 89851
6 81968
1 492737
5 493598
*/
}


signed main() { return Legendgod::main(), 0; }//


```

但是我们可不能只看这点功能，之前说了 `order_of_key` 和 `find_by_order` 是根据 `tree_order_statistics_node_update` 来的，我们可以自己写这个更新函数。

```cpp
template<class Node_CItr,class Node_Itr,class Cmp_Fn,class _Alloc>
struct my_node_update
{
    typedef my_type metadata_type;
    void operator()(Node_Itr it, Node_CItr end_it)
    {
        ...
    }
};
```

其中 `my_type` 是自己的类型，比如说 `int, Node, pair<int, int>` 之类的。

`Node_Itr it` 为调用该函数的元素的迭代器，`Node_CItr end_it` 可以 `const` 到叶子节点的迭代器，`Node_Itr` 有以下的操作：

1. `get_l_child(), get_r_child()`  获取左右儿子。

2. `get_metadata()` 获取该节点的信息。

3. `**it` 获取 `it` 的信息。

现在我们学会了更新，那么我们该如何自己写操作呢？`node_update`所有`public`方法都会在树中公开。如果我们在`node_update`中将它们声明为`virtual`，则可以访问基类中的所有`virtual`。所以，我们在类里添加以下内容：

```cpp
virtual Node_CItr node_begin() const = 0;
virtual Node_CItr node_end() const = 0;
```

```cpp
template<class Node_CItr,class Node_Itr,class Cmp_Fn,class _Alloc>
struct my_node_update
{
    typedef int metadata_type;
    int order_of_key(pair<int,int> x)
    {
        int ans = 0;
        Node_CItr it = node_begin();
        while(it ! =node_end())
        {
            Node_CItr l = it.get_l_child();
            Node_CItr r = it.get_r_child();
            if(Cmp_Fn()(x,**it))
                it = l;
            else
            {
                ans ++;
                if(l != node_end()) ans += l.get_metadata();
                it = r;
            }
        }
        return ans;
    }
    void operator()(Node_Itr it, Node_CItr end_it)
    {
        Node_Itr l = it.get_l_child();
        Node_Itr r = it.get_r_child();
        int left = 0,right = 0;
        if(l != end_it) left = l.get_metadata();
        if(r != end_it) right = r.get_metadata();
        const_cast<int&>(it.get_metadata()) = left + right + 1;
    }
    virtual Node_CItr node_begin() const = 0;
    virtual Node_CItr node_end() const = 0;
};
```

---

### 堆

```cpp
__gnu_pbds ::priority_queue<T, Compare, Tag, Allocator>
```

#### 模板形参

- `T`: 储存的元素类型
- `Compare`: 提供严格的弱序比较类型
- `Tag`: 是 `__gnu_pbds` 提供的不同的五种堆，Tag 参数默认是 `pairing_heap_tag` 五种分别是：
- `pairing_heap_tag`：配对堆 官方文档认为在非原生元素（如自定义结构体/`std :: string`/`pair`) 中，配对堆表现最好
- `binary_heap_tag`：二叉堆 官方文档认为在原生元素中二叉堆表现最好，不过我测试的表现并没有那么好
- `binomial_heap_tag`：二项堆 二项堆在合并操作的表现要优于二叉堆，但是其取堆顶元素操作的复杂度比二叉堆高
- `rc_binomial_heap_tag`：冗余计数二项堆
- `thin_heap_tag`：除了合并的复杂度都和 Fibonacci 堆一样的一个 tag
- `Allocator`：空间配置器，由于 OI 中很少出现，故这里不做讲解

```cpp
__gnu_pbds ::priority_queue<int> 
__gnu_pbds::priority_queue<int, greater<int> >
__gnu_pbds ::priority_queue<int, greater<int>, pairing_heap_tag>
__gnu_pbds ::priority_queue<int>::point_iterator id; // 迭代器
// 在 modify 和 push 的时候都会返回一个 point_iterator，下文会详细的讲使用方法
id = q.push(1);
```

#### 成员函数

- `push()`: 向堆中压入一个元素，返回该元素位置的迭代器。
- `pop()`: 将堆顶元素弹出。
- `top()`: 返回堆顶元素。
- `size()` 返回元素个数。
- `empty()` 返回是否非空。
- `modify(point_iterator, const key)`: 把迭代器位置的 `key` 修改为传入的 `key`，并对底层储存结构进行排序。
- `erase(point_iterator)`: 把迭代器位置的键值从堆中擦除。
- `join(__gnu_pbds :: priority_queue &other)`: 把 `other` 合并到 `*this` 并把 `other` 清空。

---

只有 `pairing_heap_tag` 比较快，不然 `std::priority` 就已经很不错了。

根据试验测试，配对堆对于图稍微强的一点的情况有较为明显的优化：

[Problem - D - Codeforces](https://codeforces.com/contest/843/problem/D)

```cpp
#include <bits/stdc++.h>
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_pbds;
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

using namespace Read;

const int maxn = 2e5 + 5;
int n, m, Q;
vector<pair<int, int> > vc[maxn];

void add(int u,int v,int i) {
    vc[u].push_back({v, i});
}

typedef long long int64;
const int64 inf = 1e15;
int64 d[maxn], w[maxn];
struct Node {
    int id; int64 dis;
    int operator < (const Node &z) const {
        return dis > z.dis;
    }
};

__gnu_pbds :: priority_queue<Node> q;
int vis[maxn];

void Dij() {
    while(!q.empty()) q.pop();
    fill(d + 1, d + n + 1, inf);
    d[1] = 0;
    q.push({1, 0});
    while(!q.empty()) {
        int u = q.top().id; q.pop();
        if(vis[u]) continue;
        vis[u] = 1;
        for(auto i : vc[u]) {
            int to = i.first; int64 wt = w[i.second];
            if(d[to] > d[u] + wt) {
                d[to] = d[u] + wt;
                if(!vis[to]) q.push({to, d[to]});
            }
        }
    }
}

queue<int> buc[maxn];

int64 ad[maxn];

signed main() {
	int i, j;
    r1(n, m, Q);
    for(i = 1; i <= m; ++ i) {
        int u, v;
        r1(u, v, w[i]);
        add(u, v, i);
    }
    Dij();
    for(i = 1; i <= n; ++ i) if(d[i] == inf) d[i] = -1;
//    for(i = 1; i <= n; ++ i) printf("%d : %lld\n", i, d[i]);
    while(Q --) {
        int opt, c;
        r1(opt); if(opt == 1) { r1(c); printf("%lld\n", d[c]); }
        else {
            int c; r1(c);
            for(i = 1; i <= c; ++ i) { int x; r1(x); w[x] ++; }
            fill(ad + 1, ad + n + 1, c + 1);
            buc[ad[1] = 0].emplace(1);
            for(int _ = 0; _ <= c; ++ _) {
                for(int v; !buc[_].empty(); buc[_].pop()) {
                    v = buc[_].front();
                    if(ad[v] != _) continue;
//                    printf("%d : %d\n", _, v);
                    for(auto x : vc[v]) {
                        int to = x.first; int64 wt = w[x.second];
                        int64 tmp = d[v] + ad[v] + wt - d[to];
//                        printf("(%d ,%d) = wt = %lld\n", v, to, wt);
                        if(tmp < ad[to]) buc[ad[to] = tmp].emplace(to);
                    }
                }
            }
            for(i = 1; i <= n; ++ i) if(ad[i] != c + 1) d[i] += ad[i];
        }
    }
	return 0;
}

}


signed main() { return Legendgod::main(), 0; }//legendgod
```

---

### $\tt Hash\ Table$

就两种：

```cpp
cc_hash_table<string, int> mp1;// 平方探测
gp_hash_table<string, int> mp2;// 链式探测
```

一般来说平方探测要快一点，但是哈希冲突比较多，用处不是很明显，建议使用 `unorder_map`，当然这些都会被卡。需要用手写 $\tt hash$ 函数。

```cpp
struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        // http://xorshift.di.unimi.it/splitmix64.c
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }

    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};
//unorder_map<long long, int, custom_hash> mp;
//cc_hash_table<long long, int, custom_hash> mp;
//gp_hash_table<long long, int, custom_hash> mp;
```

> 所以，如果能用 `map` 就最好用吧。

#### 用法

- `mp1[x] = y`。

- `mp1.find(x)`。

- `map` 能用的基本都能用。


