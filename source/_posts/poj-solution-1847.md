---
title: POJ1847 Tram
date: 2019-08-25 17:54:03
tags:
  - 图论
categories:
  - POJ题解
---

### [Tram(电车)](https://vjudge.net/problem/POJ-1847)
**题目大意：**

萨格勒布的电车网络由许多交叉路口和连接其中一些的铁路组成。在每个交叉路口都有一个开关，指向从交叉路口出来的一条铁轨。当有轨电车进入交叉路口时，它只能沿开关指向的方向离开。如果驾驶员想要采取其他方式，他/她必须手动更换开关。 

当驾驶员从交叉路口A到交叉路口B进行驾驶时，他/她试图选择将最小化他/她必须手动更换开关的次数的路线。 

编写一个程序，计算从交叉路口 $A$ 到交叉路口 $B$ 所需的最小开关变化次数。 
输入
输入的第一行包含整数 $N$，$A$ 和 $B$，由单个空白字符分隔，$2 \le N \le 100,1 \le A，B \le N，N$ 是网络中的交叉点数，以及交叉点从 $1$ 到 $N$ 编号。 

以下 $N$ 行中的每一行包含由单个空白字符分隔的整数序列。第 $i$ 行中的第一个数字 $K_i（0 \le K_i \le N-1）$表示从第i个交叉点出来的轨道数。下一个 $K_i$ 数字表示直接连接到第i个交叉点的交叉点。第i个交叉点中的交换点最初指向列出的第一个交叉点的方向。 
产量
输出的第一行也是唯一一行应包含目标最小数。如果没有从 $A$ 到 $B$ 的路由，则该行应包含整数 $-1$。
```样本输入```
```cpp
3 2 1
2 2 3
2 3 1
2 1 2
```
```样本输出```
```cpp
0
```
~~翻译可能不准确,是谷歌的~~
~~自己初中水平有限,请多多包涵~~  
#### $\tt \text{题意分析}$
> 首先,电车的轨道就相当于一张图
>不妨我们将不用转动的当做0,需要转动的当做1(每个开关就相当于一条无向边)
>> 由此我们就可以将本题转换为图论最短路
#### 输入输出的分析
> 每一行的第一个数代表和它相连的点
>>第一个点就先标记为0,其他为1
####  关于代码
>#### 时间限制不严格
>>##### 各种方式都可以通过
>##### **此为通过的截图**
>![此为通过的截图](https://img-blog.csdnimg.cn/20190825174120338.PNG)

>#### 这种题可以用Floyd   ~~因为真的简单~~
>### 代码的核心部分
```cpp
	for(int k=1;k<=n;k++)
		for(int u=1;u<=n;u++)
			for(int v=1;v<=n;v++) {
					dist[u][v]=min(dist[u][v],dist[u][k]+dist[k][v]);
			}
```
> 这是一种 $O(V^3)$ 的复杂度,比赛时不推荐，但是注意第一个循环本质上就是考虑走了几条边，这个是很有用的性质。

#### $\text{SPFA}$
>#### 这是 $O(KE)$ 的复杂度，~~显然在现在已经假了~~
>>### 重点是再稀疏图上 $k=1$

代码

```cpp
#include<iostream>//https://vjudge.net/problem/POJ-1847
#include<cstring>
#include<cstdio>
#include<queue>
#include<vector>
using namespace std;
struct Edg{
	int v,c;
};
const int INF=0x3f3f3f3f;
vector<Edg> edg[205];
int dist[105];
int vis[205];
int main(){
	int a,b,n;
	while(scanf("%d%d%d",&n,&a,&b)==3){
		memset(vis,false,sizeof(vis));
		for(int i=1;i<=n;i++)
		edg[i].clear();
		for(int i=1;i<=n;i++)
		{
			int x;int w;
			scanf("%d",&x);
			scanf("%d",&w);
			edg[i].push_back(Edg{w,0});
			for(int j=2;j<=x;j++)
			{
				scanf("%d",&w);
				edg[i].push_back(Edg{w,1});
			}
		}
		queue<int> q;
		q.push(a);
		for(int i=1;i<=n;i++)
		dist[i]=INF;
		dist[a]=0;
		vis[a]=true;
		while(!q.empty()){
			int num=q.front();q.pop();
			vis[num]=false;
			for(int i=0;i<edg[num].size();i++){
				int v=edg[num][i].v;
				int cost=edg[num][i].c;
				if(dist[v]>dist[num]+cost)
				{
					dist[v]=dist[num]+cost;
					if(!vis[v])
					{
						vis[v]=true;
						q.push(v);
					}
				}
			}
		}
		if(dist[b]==INF)
		printf("-1\n");
		else
		printf("%d\n",dist[b]);
	}
	return 0;
}
```

**注意：** 不要忘记判断无解的情况。

谢谢您的观看


