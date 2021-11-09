---
title: Function 语法浅谈
date: 2021-09-14 16:18:49
tags:
categories:
  - 语法
---

$$
\textbf{Funtion}\ \text{的使用}
$$

关于 ```function```属实是一种有趣的语法。

我们常常会碰到的问题，就是我们在一个函数内部想使用其局部变量来进行一些操作。

我们常用的写法是直接定义一个函数：

```cpp
auto F = [&, i] () -> void{
  	.....  
};
```

但是如果说我们需要使用一些递归的调用，```auto```不能判断出其类型，我们就必须使用 ```function```函数。

换言之 ```function```本质上是一种类型，表示一个递归函数的指针。

```cpp
function<A(B)> dfs;
```

其中 ```A```表示返回值的类型，```B```表示要填的参数。

举一个列子，我们需要遍历以当前节点 $x$ 为根的树。

```cpp
for(int i = 1; i <= n; ++ i) {
	function<void(const int& p,int pre)> dfs;
    dfs = [&] (const int &p,int pre) {
        for(auto v : vc[p]) if(v != pre) dfs(to, p);
    }
}
```

----

除此之外其还可以成为很多类型的函数：

```cpp
struct Node : {
    int operator + (const int &a, const int &b) const {
        return a + b;
    }
}
function<int(int, int)> F = Node;
```

```cpp
auto g = [] (int a,int b) { return a - b; }
function<int(int, int)> F = g;
```

也就是静态，非静态函数，$\tt Lambda$ 表达式等。






