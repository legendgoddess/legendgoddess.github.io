---
title: "如何在文章内连 pdf"
date: 2022-3-15 21:05:00
categories:
  - 语法
---

具体语法就是：
```cpp
<embed src="pdf-link" type="application/pdf" style="overflow: auto; width: 100%; height: 600px"/>'
```

其中 $\tt pdf-link$ 表示的是路径，最好是相对路径。

比如说我的文件都存在 $\tt image$ 里面，所以我的相对路径就是 `/image/xxx.pdf`。

然后后面的部分就是调整大小，$\tt px$ 就是表示像素。

> 上面的那个就是笔者博客比较适合的情况。


