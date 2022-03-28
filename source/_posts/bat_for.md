---
title: "Bat 用 For 遍历"
date: 2022-3-28 13:41:00
categories:
  - 语法
---

之前虽然已经把博客放到 $\tt github$ 上了，但是更新的时候废话还是很多，这次直接就把这些东西全部都放到一个 $\tt bat$ 上。

首先我们需要遍历一个文件夹，直接使用最弱智的方法，就是直接进入文件夹，然后开始遍历。

这里 $\tt cmd$ 和 $\tt bat$ 是有区别的，对于 `cmd`，`for` 的参数是 `%i`，如果是 `bat` 的话就是 `%%i`。

```cpp
for %%i in (*.md) do copy %%i E:\JHB_legendgod\Blog\source\_posts\%%i
```

之后需要用到 $\tt gitbash$，我们添加 $\tt gitbash$ 里面的 $\tt cmd$ 文件夹到**环境路径**上就行了。


