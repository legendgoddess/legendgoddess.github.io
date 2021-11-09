---
title: bat 之 for 循环
date: 2021-09-23 08:18:48
tags: 
categories: 
  - 语法
---

$$
\large\tt{bat} \text{ 之 } for \text{ 循环}
$$

你可以首先记一个东西叫：$\text{setlocal enabledelayedexpansion}$ 这个东西叫做变量延迟。

> 后面的东西可以看做 $3$ 个单词来拼起来，$\tt enable\ delayed\ expansion$。

然后常用的 $\tt for$ 貌似只有两种写法。

```java
for /l %%i in (a, b, c) do (
	
)
```

还有一种就是

```java
for %%i in (a, b, c) do (

)
```

前面一种的意思就是从 $a$ 开始遍历到 $c$ 每次增加 $b$。

```cpp
for(int i = a; i <= c; i += b)
```

后面只是单纯遍历 $a, b, c$ 这几个数。

举个例子比如说我要创建 $0 \sim 9$ 的文件，同时里面各有其文件标号的数字。

```java
@echo off
for /l %%i in (0, 1, 9) do (
echo %%i > %%i.txt
)
```

如果说是在 $0, 1, 9$ 的文件夹追加 $0, 1, 9$ 这些数字呢？

```java
@echo off
for %%i in (0, 1, 9) do (
echo %%i >> %%i.txt
)
```

----

还有一个问题，如果说要每次输出当前文件加到文件，同时输出其是第几个遍历到的呢？

我们会需要一个变量在 $\tt for$ 中动态自增。

```java
@echo off
setlocal enabledelayedexpansion
set /a v=1
for %%i in (*.*) do (
	echo %%i
	echo Case : !v!
	set /a v+=1
)
```






