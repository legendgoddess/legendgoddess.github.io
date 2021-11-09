---
title: "高速对拍（解决一切对拍慢的问题）"
date: 2021-09-23 07:59:59
tags: 
categories: 
  - 语法
---

<h3><center>高速对拍</center></h3>

看了一下机房里面的同学对于对拍基本上有几种写法：

- 直接用 $\tt c++$ 搞一个对拍
- 写 $3$ 个程序，之后用一个 $\tt bat$ 来整合
- 每次通过 $\tt bat$ 中的 $\tt <$ 进行传入随机种子（其实也就是拍了第几组），这个效率还是挺高的。 
- ~~根本不屑于对拍~~

其中很多的写法都是 ```Sleep(1000)```也就是意味着每秒最多只能对拍一次。

网上还有一种比较主流的写法，就是每次调用系统的内存，因为这个是动态的进行对拍。但是显然后者会有较大的可能重复导致效率不高。

于是就有了某些优秀写法：

```cpp
auto *it = new unsigned int;
srand(time(0) ^ (*it));
```

也就是每次取一个新的内存地址，然后和系统时间进行某些操作，可以尽可能保证其正确性。

> 说人话就是平时应该够用了。

---

但是有些时候对于一个程序不是很确定的时候，我们可以尝试使用更加有些的种子。

具体来说我们可以使用某些精度较高的种子：

```cpp
auto tm = std::chrono::high_resolution_clock::now();
tm.time_since_epoch().count();
```

为了方便记忆，前面那个就拆开来记好了。

后面那个就是自从开始的时间。

然后用什么 ```mt19937```来搞事情之类的。

写个简单的板子吧：

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int i, j;
    auto tm = std::chrono::high_resolution_clock::now();
    mt19937 dis(tm.time_since_epoch().count());
    uniform_int_distribution<int> ge(0, 1e9);
    printf("%d %d\n", ge(dis), ge(dis));
}
```






