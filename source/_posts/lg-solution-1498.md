---
title: Luogu1498 南蛮图腾
date: 2019-12-09 12:44:23
tag:
  - 模拟
categories:
  - 洛谷题解
---
## [原题链接](https://www.luogu.com.cn/problem/P1498)
## 题意分析
> #### 根据题目可以发现，每一个为n的图腾都可以由3个n-1大小的图腾拼成
>> 所以我们可以根据这一点进行分开计算
## 建议
### 1.建议倒着处理，较为方便
### 2.数组的初始值应为
```
' '//空格
```
### 防止初始值为NULL出错
## Code的处理
### 1.因为图腾最小为1是所以可以进行打表处理
### 2.根据样例可以判断出每一个图形的宽度~~wide~~是成倍增长的
> #### 所以将wide设为初始长度 wide=4
### 3.可以运用dep来保存第几个需要复制的图形
   ~~总共只要复制n-1次，因为第一次打表了~~
### 4.重点来了
>### 每次复制有两个要点
>> #### 将左侧复制和将右侧复制
>> #### 每次复制的深度为 wide/2
>> #### 每次复制的长(宽)度为 wide
>### 所以可以推出公式
```cpp
	mp[i+wide/2][j+wide/2]=mp[i][j]
	mp[i][j+wide]=mp[i][j]
```
>###   空格是成2倍增加的，所以把之前的图形向右移，之后再复制
### 5.输出
>#### 因为一开始是倒着存储的所以输出时也应该倒着输出
>>### wide的处理必须注意
## Code
```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn=2048;
char mp[maxn][maxn],n;
int read(){
	char ch;
	ch=getchar();
	int x=0,f=1;
	for(;!isdigit(ch);ch=getchar()) if(ch=='-') f=-1;
	for(;isdigit(ch);ch=getchar()) x=(x<<1)+(x<<3)+(ch^48);
	return f*x;
}
int main(){
	memset(mp,' ',sizeof(mp));
	int wide=4,dep=1;
	int i,j; 
	n=read();
	mp[0][0]=mp[1][1]='/',mp[0][1]=mp[0][2]='_',mp[0][3]=mp[1][2]='\\';
	//倒着复制 
	while(dep<n){//复制次数==n-1 
		for(i=0;i<wide/2;i++)
		for(j=0;j<wide;j++)
		mp[i+wide/2][j+wide/2]=mp[i][j+wide]=mp[i][j];//!!
		wide*=2,dep++;
	}
	for(i=wide/2-1;i>=0;i--)//!!
	{
		for(j=0;j<wide;j++)
		cout<<mp[i][j];
		puts("");
	}
	return 0;
} 
```
## 写法2 递归
## 推荐题目[P5461 赦免战俘](https://www.luogu.com.cn/problem/P5461)
### 我就不赘述了
```cpp
#include<bits/stdc++.h>
#define ll long long
using namespace std;
const ll maxn=2048;
char mp[maxn][maxn],n;
int dis[20];
ll read(){
	char ch;
	ch=getchar();
	ll x=0,f=1;
	for(;!isdigit(ch);ch=getchar()) if(ch=='-') f=-1;
	for(;isdigit(ch);ch=getchar()) x=(x<<1)+(x<<3)+(ch^48);
	return f*x;
}
void dfs(int x,int y,int sum){
	if(sum==1){
		mp[x][y]='/';
		mp[x][y+1]='_';//不是'-' 
		mp[x][y+2]='_';
		mp[x][y+3]='\\';
		mp[x-1][y+1]='/';
		mp[x-1][y+2]='\\';
		//根据第一个的样式复制
		//推荐题目 P5461 赦免战俘
		//同样的方法，只是单位变了 
		return ;
	}
	dfs(x,y,sum-1);//处理第n-1个图案
	dfs(x,y+dis[sum],sum-1);//向右移动dis[sum]位 
	//在这里注意， 进行一次单位的变换，由之前的n-2变为n-1
	//再进行复制 
	dfs(x-dis[sum]/2,y+dis[sum]/2,sum-1);
	// n-1个图案的深度 n-1图案的宽度 前一个图形 
}
int main(){
	memset(mp,' ',sizeof(mp));
	int i,j; 
	n=read();
	dis[0]=1;
	for(i=1;i<=n;i++)
	dis[i]=dis[i-1]*2;//图形的宽 
	dfs(dis[n],1,n);
	for(i=1;i<=dis[n];i++){
		for(j=1;j<=dis[n]*2;j++)
		cout<<mp[i][j];
		puts("");
	}
	return 0;
} 
```

