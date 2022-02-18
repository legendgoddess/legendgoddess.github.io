---
title: 如何使用 Gitbash
date: 2022-2-18 20:36:20
tags:
categories:
  - 语法
---

<h3><center>如何使用 Gitbash</center></h3>

首先你需要一个[下载地址](https://desktop.github.com/)。

然后我们来和自己的 $\tt github$ 账号进行连接，使用 $\tt SSH$。

> 我们一下的操作都是在 $\tt gitbash$ 里面进行，也就是鼠标右键出现的那里进去的。

首先检查自己电脑的 $\tt SSHkey$ 是否存在 `$ cd ~/.ssh` 然后使用 `$ ls -al` 检查是否存在。

> 如果连文件夹都不存在的话那肯定是没有的。

如果你需要创建一下 $\tt SSHkey$ 的话，我们使用 `$ ssh-keygen -t rsa -C your-email` 然后一直回车就可以了。

> $\tt your-email$ 就是直接输入你的邮箱。

然后我们找到你保存的文件地址，将 $\tt public$ 的部分复制下来。

我们点到 $\tt github$ 的主页，鼠标放在头像下面，点击 $\tt setting$ 点击 $\tt SSH$ 设置，加入新的钥匙，名字就随便取了，把之前的部分复制在下面的框里。

之后我们检查一下 `ssh -T git@github.com` 如果成功了就可以了。

然后我们使用 `git clone 你的地址`。

> 其中地址就是你项目，`code` 部分点开的地址。

然后你进入你克隆的文件夹，你的 $\tt Gitbash$ 会有一个 $\color{blue}\tt master$。

---

如果你要上传文件的话 `git add -A`。

之后 `git commit`。

> 如果出现了不成功的情况，其会显示帮助，也就是让你将你 $\tt Github$ 的名字和绑定的邮箱输入。
> 
> 之后在进行上述操作即可。

我们随便写一些评论就可以了。

最后 `git push` 即可。

你就可以在你的博客里面看到了。
