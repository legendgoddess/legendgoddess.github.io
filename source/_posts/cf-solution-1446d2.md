---
title: "CF1446D2 Frequency Problem (Hard Version)"
date: 2021-08-29 09:08:20
tags:
  - 贪心
  - 构造
categories:
  - CF题解
---

### [CF1446D2 Frequency Problem (Hard Version)](https://www.luogu.com.cn/problem/CF1446D2)

讲一种 $O(n)$ 的神仙做法，代码转载于 [$gmh77$ 神仙](https://www.cnblogs.com/gmh77/p/14006390.html)。

> 说实话这个我也不懂算不算转载，如果有问题可以直接私聊我。

考虑对于最终答案来说，我们重复计算不符合条件的比较小的区间是不影响的。

所以我们可以考虑对于每一个非众数进行计算。

- 计算只有一个众数的情况

> 虽然说可能出现了两个非众数，但是肯定存在比其更大的区间。

- 计算多个众数的情况，我们不妨记录一下众数的位置每次来跳众数，每一次更新一下当前值为 $s$ 的非众数的出现次数。也就是从左到右进行枚举。

考虑对于非众数进行圈定一个区间，同时记录当前值 $s$ 出现的最靠右的位置和其经过的众数，为了防止数组冲突可能需要额外处理一下。

```cpp
#include <bits/stdc++.h>
#define fo(a,b,c) for (a=b; a<=c; a++)
#define fd(a,b,c) for (a=b; a>=c; a--)
#define max(a,b) (a>b?a:b)
#define min(a,b) (a<b?a:b)
#define ll long long
//#define file
using namespace std;

int Sum[200002],sum[200002],a[200002],L[200002],R[200002],n,i,j,k,l,mx,mx2,ans;
int ls[200002],d[800001][2],b[200002],c[200002],f[800001],I[200002],id[200002];

int get(int t,int x)
{
	if (f[t]<=0) return -f[t];
	return I[f[t]+(x-d[t][0])];
}

void work(int i,int s)
{
	int j,k,l;
	l=sum[i]-sum[ls[s]]; // 1 的个数，这次和上次之间的
	while (l && b[s]<=R[s])
	{
		k=get(b[s],c[s]),ans=max(ans,(I[id[s]+1]-1)-k);//只出现 1 次的贡献
		if (c[s]==d[b[s]][1]) ++b[s];
		--l,++c[s],++id[s];// 每次向后跳，而且保证其不能超过当前已经有了的数
	}
	// c[s] 表示值 s 已经经过了几个众数
	if (b[s]>R[s]) ++R[s],d[R[s]][0]=d[R[s]-1][1]+1,d[R[s]][1]=c[s]+l,f[R[s]]=id[s],id[s]+=l,c[s]+=l;// id[s] = c[s] = l
    /*
    众数是 1，不是众数是 -1。

    可以发现 d 存了两个值。
    不妨猜测是 1 的数量，和当前值的数量
    d[R[s]][0] 表示经过了几个当前的数。

    */
	k=get(b[s],c[s]),ans=max(ans,(i-1)-k);// 这个更新肯定不会更劣，如果说当前众数比较少，那么因为是众数所以后面肯定会更新覆盖
	/*
    简单来说每次寻找一个最大的 s 的数量和众数的数量相同的区间
    为了保证不会出现众数数量比当前的数的数量多的情况，使用 f 数组向后面滚动
	*/
	if (i<=n)// 不是考虑整个区间的
	{
		if (c[s]==d[b[s]][0]) // 数量相同
		{
			if (b[s]==L[s]) //
			{
				--L[s];// 区间可以扩展
				d[L[s]][0]=d[L[s]][1]=d[L[s]+1][0]-1;
				f[L[s]]=-i;
			}
			--b[s];// 因为回溯了，所以之前的贡献也应当减去
		}
		--c[s];// 回溯一个位置
	}
	ls[s]=i; // 当前颜色最右边的位置
}
/*
可以发现 L 是不会改变值的
容易想到这个就是整个区间最左边的时候才有可能产生，也就是可以向左继续延伸
*/


int main()
{
	#ifdef file
	freopen("CF1446D.in","r",stdin);
//	freopen("b.out","w",stdout);
	#endif

	scanf("%d",&n);
	fo(i,1,n)
	{
		scanf("%d",&a[i]),++Sum[a[i]];
		if (Sum[a[i]]>mx) mx=Sum[a[i]],mx2=a[i];
	}
	fo(i,1,n+1) sum[i]=sum[i-1]+(a[i]==mx2); // 找众数

	k=l=0;// 这个 l 只是单纯用来防止算重复
	fo(i,1,n)
	if (i!=mx2) //枚举值域
	b[i]=l+(Sum[i]+1),L[i]=R[i]=b[i],c[i]=0,d[b[i]][0]=d[b[i]][1]=0,l+=Sum[i]*2+2;
	fo(i,1,n) if (a[i]==mx2) I[++k]=i; // 记录位置

	fo(i,1,n) if (a[i]!=mx2) work(i,a[i]);
	fo(i,1,n) if (i!=mx2) work(n+1,i);
	printf("%d\n",ans);

	fclose(stdin);
	fclose(stdout);
	return 0;
}

```




