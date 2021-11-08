---
title: Luogu2882 [USACO07MAR]Face The Right Way
date: 2020-09-17 09:33:33
tags:
categories:
  - 洛谷题解
---
## [P2882 [USACO07MAR]Face The Right Way](https://www.luogu.com.cn/problem/P2882)
### 说实话这一题写了半个上午，主要是位运算的问题
> ### 首先要明白的是

>	1^1 = 0

>	1^0 = 1

>	1^0 = 1

>	0^0 = 0

> #### 由此可以看出如果一个一位数（二进制） _异或0_ 就是它
>> ### 本身
> #### 由此可以看出如果一个一位数（二进制） _异或1_ 就是它
>> ### 相反数


>#### 本题的牛只有两个状态，而且每次旋转的长度是固定(枚举k)的
>#### 所以枚举n和k是无法省略的
> ### 第三层循环可以**差分**优化

## 具体做法
> 建立数组b[ ],表示当前（差分）位置的方向情况
>>~~转向两次等于没转~~
> 用now代表当前的牛是否转向
> ### 我们只要判断（now ^ v[ i ]）是否等于0
>> 若等于0，则进行操作

> 一次操作是长度为k的区间，起点为i
>> 根据差分,影响到的点为 **i,i+k**

>> 将b[ i ] 和 b[i + k]进行转向操作（^1）,m++即可

> ### 注意判断是否出界
```cpp
	if(i + k - 1 > n) {失败}
```

## Code
```cpp
#include<bits/stdc++.h>
using namespace std;

const int maxn = 5e3 + 5;
const int inf = 0x7fffffff;
int read(); 
void write(int x);
#define r1 read()
#define w1(x) write(x)
int ansm = inf,ansk = inf;
int n, m, k = 0x3f3f3f3f;
int v[maxn];
int s[maxn];
int s1[maxn];
int f[maxn];
int main() {
	n = r1;
	int i, j;
	char c;
	for(i = 1;i <= n; i++) {
		cin >> c;
		if(c == 'B')
		v[i] = 0;
		else
		v[i] = 1;
		s1[i] = v[i] - v[i - 1];
	}
//	for(i = 1;i <= n; i++)
//	cout << s[i]<<" ";
	int l = 1,r = 1;
	bool flag = false;
	int k;
	for(k = 1;k <= n; k++) {
		bool flag = 1;
		int now = 0,sum =0;
		memset(f,0,sizeof(f));
		for(i = 1;i <= n; i++) {
			now ^= f[i];
			if(!(now ^ v[i])) {
				if(k + i - 1 > n) {
					flag = 0;
					break;
				}
				f[i+k]^=1;
				now^=1;
				sum++;
			}
		}
		if(flag) {
//			for(i = 1;i <= n; i++) {
//				cout << s[i]<<" ";
//			}
//			puts("");
			if(ansm > sum) {
//				cout << sum<<" "<<ansm<<" ";
//				puts("");
				ansm = sum;
				ansk = k;
			}
		}
	}
	printf("%d %d\n",ansk,ansm);
	return 0;
}

int read() {
	char c = getchar();
	int x = 0,f = 1;
	for(; !isdigit(c);c = getchar()) if(c == '-')f = -1;
	for(; isdigit(c);c = getchar ()) x = (x << 1) + (x << 3) + (c ^ 48);
	return f * x;
}
void write(int x) {
	if(x > 9)
	write(x/10); 
	putchar(x%10 +'0');
}
```

