---
title: Test
data: 2022-3-22 14:43:00
password: fuyimin
---

## 模拟赛

| 题目名称    | 圆盘时钟      | 吃吃串     | 最小距离         | 修路        |
| ------- | --------- | ------- | ------------ | --------- |
| 题目类型    | 传统题       | 传统题     | 传统题          | 传统题       |
| 可执行文件名  | clock     | ccx     | distance     | track     |
| 输入文件名   | clock.in  | ccx.in  | distance.in  | track     |
| 输出文件名   | clock.out | ccx.out | distance.out | track.out |
| 每个测试点时限 | 1s        | 1s      | 1s           | 1s        |
| 运行内存上限  | 256MB     | 256MB   | 512MB        | 512MB     |

编译命令：

| C++  | -Wl,--stack=1073741824 -O2 -std=c++14 |
| ---- | ------------------------------------- |
| 其它语言 | rm -r -f                              |

注意事项：

1. 文件名（程序名和输入输出文件名）必须使用英文小写。
2. C++ 中函数 main() 的返回值类型必须是 int，程序正常结束时的返回值必须是 0。 
3. 若无特殊说明，结果比较方式为忽略行末空格、文末回车后的全文比较。
4. 选手应将各题的源程序放在选手文件夹内，**不要建立子文件夹**。

<div STYLE="page-break-after: always;"> </div>

### 玩具谜题(toy)

#### 题目描述

小南有一套可爱的玩具小人,它们各有不同的职业。

有一天,这些玩具小人把小南的眼镜藏了起来。小南发现玩具小人们围成了一个圈,它们有的面朝圈内,有的面朝圈外。如下图：

![image-20220322142108824](C:\Users\DeaphetS\AppData\Roaming\Typora\typora-user-images\image-20220322142108824.png)

这时 $\text{singer}$ 告诉小南一个谜题：“眼镜藏在我左数第 3 个玩具小人的右数第 1 个玩具小人的左数第 2 个玩具小人那里。”

小南发现,这个谜题中玩具小人的朝向非常关键,因为朝内和朝外的玩具小人的左右方向是相反的：面朝圈内的玩具小人,它的左边是顺时针方向,右边是逆时针方向；而面向圈外的玩具小人,它的左边是逆时针方向,右边是顺时针方向。

小南一边艰难地辨认着玩具小人,一边数着：

“ $\text{singer}$ 朝内,左数第 $3$ 个是 $\text{archer}$ 。

“ $\text{archer}$ 朝外,右数第 $1$ 个是 $\text{thinker}$ 。

“ $\text{thinker}$ 朝外,左数第 $2$ 个是 $\text{writer}$ 。

“所以眼镜藏在 $\text{writer}$ 这里！ ”

虽然成功找回了眼镜,但小南并没有放心。如果下次有更多的玩具小人藏他的眼镜,或是谜题的长度更长,他可能就无法找到眼镜了。所以小南希望你写程序帮他解 决类似的谜题。这样的谜题具体可以描述为：

有 $n$ 个玩具小人围成一圈,已知它们的职业和朝向。现在第 $1$ 个玩具小人告诉小南一个包含 $m$ 条指令的谜题,其中第 $i$ 条指令形如“左数/右数第 $s_i$ 个玩具小人”。你需要输出依次数完这些指令后,到达的玩具小人的职业。

#### 输入格式

输入的第一行包含两个正整数 $n,m$，表示玩具小人的个数和指令的条数。

接下来 $n$ 行，每行包含一个整数和一个字符串，以<u><strong>逆时针</strong></u>为顺序给出每个玩具小人的朝向和职业。其中 $0$ 表示朝向圈内，$1$ 表示朝向圈外。保证不会出现其他的数。字符串长度不超过 $10$ 且仅由小写字母构成，字符串不为空，并且字符串两两不同。整数和字符串之间用一个空格隔开。

接下来 $m$ 行，其中第 $i$ 行包含两个整数 $a_i,s_i$，表示第 $i$ 条指令。若 $a_i=0$，表示向左数 $s_i$ 个人；若 $a_i=1$，表示向右数 $s_i$ 个人。保证 $a_i$ 不会出现其他的数，$1 \le s_i < n$。

#### 输出格式

输出一个字符串，表示从第一个读入的小人开始，依次数完 $m$ 条指令后到达的小人的职业。

#### 样例数据

*input1*

```
7 3
0 singer
0 reader
0 mengbier 
1 thinker
1 archer
0 writer
1 mogician 
0 3
1 1
0 2
```

*output1*

```
writer
```

*input2*

```
10 10
1 C
0 r
0 P
1 d
1 e
1 m
1 t
1 y
1 u
0 V
1 7
1 1
1 4
0 5
0 3
0 1
1 6
1 2
0 8
0 4
```

*output2*

```
y
```

<h3>【子任务】</h3>
<p>子任务会给出部分测试数据的特点。如果你在解决题目中遇到了困难，可以尝试只解决一部分测试数据。</p>
<p>每个测试点的数据规模及特点如下表：</p>
<table class="table table-bordered table-text-center table-vertical-middle"><thead><tr><th rowspan="1">测试点</th><th rowspan="1">$n$</th><th rowspan="1">$m$</th><th rowspan="1">全朝内</th><th rowspan="1">全左数</th><th rowspan="1">$s_i=1$</th><th rowspan="1">职业长度为 $1$</th></tr></thead><tbody><tr><td rowspan="1">$1$</td><td rowspan="16">$=20$</td><td rowspan="16">$=10^{3}$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\surd$</td><td rowspan="4">$\surd$</td><td rowspan="8">$\surd$</td></tr><tr><td rowspan="1">$2$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$3$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\times$</td></tr><tr><td rowspan="1">$4$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$5$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\surd$</td><td rowspan="4">$\times$</td></tr><tr><td rowspan="1">$6$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$7$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\times$</td></tr><tr><td rowspan="1">$8$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$9$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\surd$</td><td rowspan="4">$\surd$</td><td rowspan="12">$\times$</td></tr><tr><td rowspan="1">$10$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$11$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\times$</td></tr><tr><td rowspan="1">$12$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$13$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\surd$</td><td rowspan="8">$\times$</td></tr><tr><td rowspan="1">$14$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$15$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\times$</td></tr><tr><td rowspan="1">$16$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$17$</td><td rowspan="4">$=10^{5}$</td><td rowspan="4">$=10^{5}$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\surd$</td></tr><tr><td rowspan="1">$18$</td><td rowspan="1">$\times$</td></tr><tr><td rowspan="1">$19$</td><td rowspan="1">$\surd$</td><td rowspan="2">$\times$</td></tr><tr><td rowspan="1">$20$</td><td rowspan="1">$\times$</td></tr></tbody></table><p>其中一些简写的列意义如下：</p>
<ul><li>全朝内：若为“$\surd$”，表示该测试点保证所有的玩具小人都朝向圈内；</li>
<li>全左数：若为“$\surd$”，表示该测试点保证所有的指令都向左数，即对任意的 $1 \le i \le m$，$a_i = 0$；</li>
<li>$s_i=1$：若为“$\surd$”，表示该测试点保证所有的指令都只数 $1$ 个，即对任意的 $1 \le i \le m$，$s_i = 1$；</li>
<li>职业长度为 $1$：若为“$\surd$”，表示该测试点保证所有玩具小人的职业一定是一个长度为 $1$ 的字符串。</li>

<div STYLE="page-break-after: always;"> </div>

### 吃吃串(ccx)

#### 题目描述

liu_runda很饿.他决定出门撸串.

liu_runda所撸的串可以认为是由大写字母A-Z组成的一个序列,也就是说,liu_runda吃的其实是字符串.
liu_runda觉得,字符串简直好吃极了,串串香.但是每次吃串的时候,liu_runda只会吃一种长度为m的串,一共吃n根.也就是说,liu_runda按照顺序吃了mn个大写字母.

比如说,liu_runda吃长度为m=8的串KEYBOARD,吃的次数n=5,那么他会吃下KEYBOARDKEYBOARDKEYBOARDKEYBOARDKEYBOARD这样连续的40个大写字母,也可以认为liu_runda吃了一根长度为40的字符串

接下来我们定义子串substr(L,R)是liu_runda吃的第L到R个字母组成的字符串.比如对于刚才的长度为40的串,substr(1,4)=KEYB,substr(8,10)=DKE.

liu_runda发现一个很有趣的现象,就是有的时候他吃的最前面x个大写字母和最后面x个大写字母会组成相同的子串,也就是substr(1,x)和substr(nm-x+1,nm)完全相同.x=nm的时候这一定成立,但是liu_runda觉得这种情况没什么意思,所以他要求1<=x<nm.在这个条件下,liu_runda希望选手找出最大的x.如果不存在这样的x,选手需要输出-1.同时,选手需要处理多组询问,因为liu_runda吃了很多天的串.(可能liu_runda以后出的题会有道题叫”减减肥”)

#### 输入格式

每个输入文件包含多组测试数据.输入文件的第一行为一个整数T,表示数据组数.接下来T行,每行表示一组测试数据

每行一开始,两个空格隔开的数字n,m,含义见【题目描述】.之后是一个长度为m的字符串.

#### 输出格式

T行,每行一个整数表示答案.

#### 样例数据

*input*

```
10
1 3 LKL
2 5 QCOEJ
3 11 PHTUOTBSJBP
4 3 CRN
5 3 XXX
6 10 HTHTHTHTHT
7 5 OVEZO
8 5 DYIDY
9 6 ULTULT
10 4 UUKD
```

*output*

```
1
5
22
9
14
58
30
35
51
36
```

#### 数据规模与约定

对全部测试数据,n<=1e6,m<=1e6,T<=10,输入文件中的所有m之和不超过5e6

第1,2个测试点,m=1

第3,4个测试点,给出的字符串中只含有字母’A’

第5,6,7,8,9,10,11个测试点,满足n*m<=1e6

第11,12,13,14,15个测试点,满足m<=100

第16,17,18,19,20个测试点,无特殊限制     

<div STYLE="page-break-after: always;"> </div>

### 最小距离(distance)

#### 题目描述

给定一张n个点m条边的带边权连通无向图，其中有p个点是特殊点。

对于每个特殊点，求出它到离它最近的其它特殊点的距离。

#### 输入格式

第一行三个整数n,m,p，第二行p个整数x1~xp表示特殊点的编号。接下来m行每行三个整数u,v,w表示一条连接u和v，长度为w的边。

#### 输出格式

输出一行p个整数，第i个整数表示xi的答案。

#### 样例数据

*input*

```
5 6 3
2 4 5
1 2 4
1 3 1
1 4 1
1 5 4
2 3 1
3 4 3
```

*output*

```
3 3 5
```

#### 数据规模与约定

对于前10%的数据，p<=10。

对于前40%的数据，n=40000，m=50000，p<=15000

还有5%的数据，n=45000，m=50000，p=45000

对于100%的数据，1<=n,m<=2*$10^5$，2<=p<=n，1<=xi<=n，xi互不相同，1<=u,v<=n，1<=w<=$10^9$。

<div STYLE="page-break-after: always;"> </div>

### 修路(track)

#### 题目描述

给定一棵大小为n的树，树边有边权，现在```CDX```想要在树上修m条路。

一条路指的是树上的一条简单链（每个边经过一次），路的长度定义为链上边的权值和。

```CDX```想要知道，若要让这m条路两两之间没有相同的边（可以有相同的点），那么最短的那条路的长度最大可以是多少。

#### 输入格式

第一行两个整数n,m，表示树的大小和要修的道路条数。

接下来n-1行，每行三个数字a,b,c，描述树上的一条权值为c的边(a,b)。

#### 输出格式

一行一个整数，即长度最小的路长度的最大值。

#### 样例数据

*input1*

```
7 1
1 2 10
1 3 5
2 4 9
2 5 8
3 6 6
3 7 7
```

*output1*

```
31
```

*input2*

```
9 3
1 2 6
2 3 3
3 4 5
4 5 10
6 2 4
7 2 9
8 4 7
9 4 4
```

*output2*

```
15
```

#### 样例解释

样例一相当于求树上最长链，选取4-2-1-3-7即可。

样例二修的三条路分别为：1-2-7，5-4-8，6-2-3-4-9。长度分别为15,17,16。

#### 数据规模与约定

![](D:\C++\coach\题目\20220312test\数据范围.png)

分支不超过3即每个点的度数均<=3。

对于所有的数据，保证$2\le n\le 50000, 1\le m\le n-1,1\le a_i,b_i\le n,1\le c_i\le 10000$。