---
title: "[HAOI2007]反素数"
date: 2019-08-24 21:44:43
tags:
  - 数论
categories:
  - 省选题解
---

# [P1463 [POI2001][HAOI2007]反素数](https://www.luogu.org/problem/P1463) 题解
## 题意分析
>首先这是一个数论题

## $\tt Solution$
> ###### 根据数据分析得出 $2^9<\text{前十个质数的乘积}$
> >##### 由此判断出所有数中所含有的质数不会超过 $10$ 个
>>###### 即 $2,3,5,7,11,13,17,19,23,29,31$
>#### 因为每个数都可以分成(质数除外)若干质数的乘积
>>### 此题中只有 $10$ 个质数
>### 那么 约数个数的公式为 $f(n)=(a1+1)\times(a2+1)\times\dots(an+1)$
>>##### $ai$ 为 $n$ 分解出来的质数的指数
>>## 从大到小排列
>>>## 因为约数相同,最小的数才一定是反质数
>#### 之后通过搜索算法找到答案即可。

---

### $Code$
```cpp
#include <iostream>
#include <cstdio>
using namespace std;
int f[]={0,2,3,5,7,11,13,17,19,21,23,29,31};//因为2*3*5*7*11...*31已经大于本题的数据
long long k,maxn,maxm;//所以约数也只有(质数) 10 个
void dfs(long long m,int ys,int xiabiao,int zhishu){// 搜索的数 约数 下标 指数
    if(m<maxm&&maxn==ys||ys>maxn){//∵约数相同最小的数才是反质数
        maxn=ys;
        maxm=m;
    }
    int j=0,nys;
    while(j<zhishu){
        j++;
        if(k/f[xiabiao]<m)
            break;
            m*=f[xiabiao];//n=p1^a1*p1^a2...*pk^ak
        nys=ys*(j+1);//函数 n的约数个数f(n)=(a1+1)*(a2+1)*...(ak+1)
        dfs(m,nys,xiabiao+1,j);
    }
}
int main()
{
    scanf("%d",&k);
    dfs(1,1,1,30);
    printf("%d\n",maxm);
    return 0;
}
```
![终于过了](https://img-blog.csdnimg.cn/20190824213958969.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoYXJwX2xlZ2VuZGdvZA==,size_16,color_FFFFFF,t_70)



