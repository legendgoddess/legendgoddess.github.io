---
title: CF756F
date: 2021-08-27 13:49:11
tags:
  - 数据结构
  - 分治
categories:
  - CF题解
---

### [CF756F](https://codeforces.com/contest/756/problem/F)
>原本以为校内有神仙要讲评这题，就赶快去做了。结果啊，是我题号记错的 $\dots$ 

**题目大意：**

给出一个字符串请解析这个字符串构成的数。

- $l - r$ 表示 $l \sim r$ 中的所有数， $8 -10$ 表示 $8910$。
- $+ x$ 表示单纯增加一个 $x$，如 $8-10+5$ 表示 $89105$。

我们称上面的这样的形式为表达式。

- 对于 $a(\text{表达式})$ 这样的形式，表示写 $a$ 遍表达式表示的数。
- 运算优先级是 $(), +, -$ 从高到低。

----

考虑建立一个表达式树，进行分治处理。

感觉这里最妙的一点就是节点的合并。

因为我们节点的合并可能是重复写很多次，我们不妨记录节点的数值和长度作为一点类。设其为 $(x, y)$。

我们分类讨论一下合并的情况：

- $a + b$。$(a_x \times 10^{b_y} + b_x, a_y + b_y)$。
- $a(b)$。$(b_x \times ({(10^{b_y})}^{a_x} - 1) \div (10^{b_y} - 1), b_y \times a_x)$。这里代码里有注释更清晰。
- $a-b$ 我们分类讨论，对于每一种长度分别进行合并。不妨设 $a, b$ 长度相同为 $n$。那么我们就要计算 $\sum_{i = 0} ^ {a - b} (b - i) \times (10^n)^i$。这个显然是一个等差等比数列。

设这个数列是 $S$，$S_i = ((-1) \times i + b) \times 10^{ni}$。

那么 $a = -i, b = b,q = 10^n$。

得到 $x = \frac{-i}{10^n - 1}, y = \frac{b + \frac{i}{10^n - 1}}{10^n - 1}$。

那么这个数列的前 $w$ 项的和就是 $10^{n(x + 1)} \times (xw + y) - y$。

---

那么说人话就是如下的柿子：
$$
(b + 1) \times \frac{10^{n \times (b - a + 1)} - 1}{10^n - 1} - \frac{(b - a + 1) 10^{n \times(b -a + 2)} - (b - a + 2) 10^{n\times(b - a + 1)} + 1}{(10^n - 1) ^ 2}
$$
由此可以发现我们的 $a, b$ 可能会出现在指数上，取模是不同的，所以我们需要对于每一个数额外存储其取模 $\varphi(mod)$ 的值。

```cpp
#include <bits/stdc++.h>
using namespace std;

const int mod = 1e9 + 7;

template <typename T,typename... Args> inline void r1(T& t, Args&... args) {
    r1(t);  r1(args...);
}

typedef long long ll;
//#define int long long
const int maxn = 1e5 + 5;
const int N = 1e5;
const int maxm = maxn << 1;

int nxt[maxn], pwd[maxn], pwu[maxn];
char s[maxn];
int n;
stack <int> st;

int ksm(int x,int mi,int mod) {
    int res(1);
    while(mi) {
        if(mi & 1) res = 1ll * res * x % mod;
        mi >>= 1;
        x = 1ll * x * x % mod;
    }
    return res;
}

struct Node {
    int x, y;
    Node(int a = 0,int b = 0) : x(a), y(b) {}
    Node operator + (const Node &z) const {
        return Node( ((ll)x * ksm(10, z.y, mod) + z.x) % mod, (y + z.y) % (mod - 1) );
    }
};

int inv(int a) {
    return a <= 1 ? a : (mod - 1ll * mod / a * inv(mod % a) % mod);
}

Node Mul(int a,int b,int ca,int cb,int ln) { // not mi, mi, len
//    printf("A = %d\n", pwd[ln] - 1);
    int A = pwd[ln], iv = inv(A - 1);
    int d = ksm(A, (cb - ca + mod) % (mod - 1), mod);
    int nx, ny;
    nx = 1ll * (b + 1) * (d - 1 + mod) % mod * iv % mod;
    nx = ( nx - 1ll * iv * iv % mod * (b - a + 1 + mod) % mod * d % mod * A % mod + mod ) % mod;
    nx = ( nx + 1ll * iv * iv % mod * (b - a + 2 + mod) % mod * d % mod ) % mod;
    nx = ( nx - 1ll * iv * iv % mod + mod) % mod;
    ny = 1ll * ln * (cb - ca + mod) % (mod - 1);
    return Node(nx, ny);
}

Node Solve(int L,int R) {
    assert(L < R);
    for(int i = L; i < R; ++ i) {
        if(s[i] == '(') { i = nxt[i]; continue; }
        if(s[i] == '+') return Solve(L, i) + Solve(i + 1, R);
    }
    if(s[R - 1] == ')') {
        int mi(0), fmi(0);
        for(int i = L; i < R; ++ i) {
            if(s[i] == '(') {
                Node A = Solve(i + 1, R - 1);
                int d = ksm(10, A.y, mod);
                int ansx, ansy;
                if(d == 1) ansx = 1ll * fmi * A.x % mod;
                else {
                    ansx = 1ll * A.x * (ksm(d, mi, mod) - 1) % mod * inv(d - 1) % mod;
                    /*
                    * 999999999999
                    / 9999
                    = 100010001000
                    */
                    ansx = (ansx + mod) % mod;
                }
                ansy = 1ll * mi * A.y % (mod - 1);
                return Node(ansx, ansy);
            }
            mi = (10ll * mi + s[i] - '0') % (mod - 1);
            fmi = (10ll * fmi + s[i] - '0') % mod;
        }
    }
    int ami(0), afmi(0);
    for(int i = L; i < R; ++ i) {
        if(s[i] == '-') {
            int bmi(0), bfmi(0);
            int la(i - L), lb(R - 1 - i);
            for(++ i; i < R; ++ i) {
                bmi = (10ll * bmi + s[i] - '0') % (mod - 1);
                bfmi = (10ll * bfmi + s[i] - '0') % mod;
            }
            if(la == lb) return Mul(afmi, bfmi, ami, bmi, la);
//            puts("ZZZz");
            Node A;
            A = Mul( afmi, (pwd[la] - 1 + mod) % mod, ami, (pwu[la] - 2 + mod) % (mod - 1), la);
//            puts("CCC");
            for(i = la + 1; i < lb; ++ i) A = A + Mul(pwd[i - 1], (pwd[i] - 1 + mod) % mod, pwu[i - 1], (pwu[i] - 2 + mod) % (mod - 1), i);
//            puts("ZZZ");
//            printf("lb = %d\n", lb);
            A = A + Mul(pwd[lb - 1], bfmi, pwu[lb - 1], bmi, lb);
//            puts("ZZZ");
            return A;
        }
        ami = (10ll * ami + s[i] - '0') % (mod - 1);
        afmi = (10ll * afmi + s[i] - '0')  % mod;
    }
    return Node(afmi, R - L);
}

signed main() {
//    freopen("S.in", "r", stdin);
//    freopen("S.out", "w", stdout);
    int i;
    scanf("%s", s);
    n = strlen(s);
    for(i = 0; i < n; ++ i) {
        if(s[i] == '(') st.push(i);
        if(s[i] == ')') {
            nxt[st.top()] = i;
            st.pop();
        }
    }
    pwu[0] = pwd[0] = 1;
    for(i = 1; i <= N; ++ i) pwd[i] = pwd[i - 1] * 10ll % mod;
    for(i = 1; i <= N; ++ i) pwu[i] = pwu[i - 1] * 10ll % (mod - 1);
    printf("%d\n", Solve(0, n).x);
 	return 0;
}
```

[$Aclink$](https://codeforces.com/contest/756/submission/127153951)






