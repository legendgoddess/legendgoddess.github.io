---
title: 单调队列（初学）总结
date: 2020-01-16 16:06:02
tags:
  - 单调队列
categories:
  - 算法
---
## 单调队列？[入门题了解一下](https://www.luogu.com.cn/problem/P2952)
## 1、定义
>单调队列顾名思义就是具有单调性的队列
>>单调性：所有元素保持同一种状态（递减 or 递增）
## 2、作用
>可以优化dp,求最值
>将元素压入队列之中,同时保持队列的单调性,之后再取出队首（最优当前解）
## 3、使用
>### [P2422 良好的感觉](https://www.luogu.com.cn/problem/P2422)

```cpp
#include<bits/stdc++.h>
#define gt getchar()
#define int long long
using namespace std;

const int maxn = 100001;
int n, l, r;
int v[maxn]; 
int sum[maxn];
int q[maxn], dp[maxn];
int x, head, tail, ans;

int read() {
	char c = gt;
	int x = 0, f = 1;
	for(; !isdigit(c); c = gt) if(c == '-') f = -1;
	for(; isdigit(c); c = gt) x = (x << 1) + (x << 3) + (c ^ 48);
	return f * x;
}
#define r1 read()

signed main() {
	n = r1;
	int i, j;
	tail = 0;
	for(i = 1; i <= n; i ++) {
		v[i] = r1;
		sum[i] = sum[i - 1] + v[i];
	}
	v[++n] = 0;
	for(i = 1; i <= n; i ++) {
		while(v[q[tail]] > v[i])
		{
			dp[q[tail]] = dp[q[tail]] + sum[i - 1] - sum[q[tail]];
			tail--;
		}
		dp[i] = sum[i] - sum[q[tail]];
		q[++tail] = i;
	}
	for(i = 1; i <= n - 1; i ++) {
		ans = max(ans, v[i] * dp[i]);
	}
	printf("%lld\n",ans);
	return 0;
} 
```

