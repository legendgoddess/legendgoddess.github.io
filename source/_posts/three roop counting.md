---
title : 三元环计数
---

<h3><center>三元环计数</center></h3>

> 就是考试的时候整了一个这个科技，我没想出来，然后无了...

具体来说就是计算图中三元团的个数。

有一个 $O(m\sqrt m)$ 的算法：

- 让度数大的点连接度数小的点。
- 枚举每个点并将其相邻的点记录匹配点为当前点 $i$。
- 枚举当前点邻居的邻居，看匹配点是否是点 $i$。

每个三元团只会被计算一次。

----

分析一下复杂度：

每个点的入度度数最大是 $O(\sqrt m)$，这里可以考虑反证：

如果大于 $\sqrt m$，那么连边就是不合法的。

所以打标记是 $O(m\sqrt m)$ 的。

---

为了保证图是一个 $DAG$ ，我们还需要钦定如果度数相同要从值小的连接向大的即可。

这样可以保证一个三元环 $(u, v, w)$ 被计算当且仅当存在边：$u \to v, u \to w, v \to w$。

---

$Code$

```cpp
for(int i = 1; i <= m; ++ i) r1(eu[i], ev[i]), ++ deg[eu[i]], ++ deg[ev[i]];

auto cmp = [&](int a,int b) -> int {
	return deg[a] > deg[b] || (deg[a] == deg[b] && a < b);
};

for(int i = 1; i <= m; ++ i) {
    if(cmp(eu[i], ev[i])) add(eu[i], ev[i]);
    else add(ev[i], eu[i]);
}

for(int i = 1; i <= n; ++ i) {
    for(int v : vc[i]) vis[v] = i;
    for(int v : vc[i]) for(int k : vc[v]) 
        ans += (vis[k] == i);
}
```




