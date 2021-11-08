---
title: 可持久化线段树（模板）
date: 2020-04-14 20:15:54
tags:
  - 线段树
  - 主席树
  - 数据结构
categories:
  - 算法
---
### 题目见[P3834](https://www.luogu.com.cn/problem/P3834)

> ##### 首先，看到数据ai如此之大，却要用线段树做，那么就必须用
> ### 离散化
>> ###### 以下为模板

```cpp
	n = r1;
	for(i = 1;i <= n; i++){
		v[i] = r1;
		b[i] = v[i];
	}
	sort(b+1,b+n+1);
	int len = unique(b+1,b+n+1)-(b+1);
	for(i = 1;i <= n;i++) {
		int x = lower_bound(b+1,b+len+1,v[i]) - b;
	}
```

> #### 再放一张关于主席树的图
>> ![](https://cdn.luogu.com.cn/upload/image_hosting/sxx63b3s.png)
>> ##### 也就是相当于将不需要改动的节点不用重新建树，只要用原来的树就行

>> ##### 如果要查询区间内有没有在某个位置放数，只需
```
tree[r].sum - tree[l-1].sum
```
### 以下是本题目的代码

```cpp
#include<bits/stdc++.h>
#define int long long
using namespace std;

int read();
#define r1 read()

const int maxn = 2e5+10, N = 1;
int n, m, k;
int v[maxn];
int b[maxn];
int root[maxn];
struct Node{
	int sum,l,r;
}tree[maxn * 20];
//是 log2（maxn） 
int tot = 0;
void update(int last,int &now,int l,int r,int x) {
	tree[++tot] = tree[last];
	now = tot;
	tree[now].sum++;
	if(l == r) return;
	int mid = (l + r) >> 1;
	if(x <= mid) {
		update(tree[last].l,tree[now].l,l,mid,x);
		//包含 mid 
	}
	else {
		update(tree[last].r,tree[now].r,mid + 1,r,x);
	}
}
int query(int x,int y,int l,int r,int k) {
	if(l == r) return l;
	int mid = (l + r) >> 1;
	//用两个tree  不然就代表两个根的值相减，毫无软用 
	int sum = tree[tree[y].l].sum - tree[tree[x].l].sum;
	//判断左子树是否在时间戳上符合条件 
	if(sum >= k) {
		//注意等号 
		return query(tree[x].l,tree[y].l,l,mid,k);
	} 
	else {
		return query(tree[x].r,tree[y].r,mid + 1,r,k-sum);
		//减去 sum是右边要找的第几个数 
	}
}
int build(int l, int r) {
	//空的树 
	int pos = ++tot;
	tree[pos].sum = 0;
	if(l == r) return l;
	int mid = (l + r) >> 1;
	tree[pos].l = build(l,mid);
	tree[pos].r = build(mid + 1,r);
	return pos;
}
signed main() {
	int i, j;
	n = r1;m = r1;
	for(i = 1;i <= n; i++) {
		v[i] = r1;
		b[i] = v[i];
	}
	sort(b+1,b+n+1);
	int len = unique(b+1,b+n+1) - (b+1);
	root[0] = build(1,len); 
	for(i = 1;i <= n;i++) {
		int x = lower_bound(b+1,b+len+1,v[i])-b;
		update(root[i-1],root[i],1,len,x);
	}
	for(i = 1; i <= m; i ++) {
		int l,r,k;
		l = r1,r = r1,k = r1;
		int id = query(root[l - 1],root[r],1,len,k);
		printf("%d\n",b[id]);
	}
	return 0;
}
int read() {
	char c = getchar();
	int x = 0,f = 1;
	for(; !isdigit(c);c = getchar()) if(c == '-') f = -1;
	for(; isdigit(c);c = getchar()) x = (x << 1) + (x << 3) + (c ^48);
	return f * x;
}
```

