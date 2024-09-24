---
title: 【题解】 P10153 「LAOI-5」膜你赛
date: 2024-09-21 19:50:43
categories: 
    - 构造
tags: 
    - 构造
    - 题解
    - 原创
mathjax: true
banner: https://api.xsot.cn/bing?jump=true
---

## $\large\mathfrak{1st.\ Preamble|}$ 前言

[题目传送门](https://www.luogu.com.cn/problem/P10153)。一道有趣的构造题。

## $\large\mathfrak{2nd.\ Solution|}$ 题解

### 基本思路

【因为】以过题数为第一关键字不升排序，
【所以】过题数数少的先离场。

【因为】以罚时数为第二关键字不降排序，
【所以】同过题数，罚时多的先离场。

【所以】存在一种构造能使所有人「爆切比赛」。

### 构造方法

把所有人按题目要求排序。

过题数相同的人合在一块，把这些块按每一块的过题数递增排列。

对于每一块内，我们把每人按 WA 数从大到小安排离场顺序。

**错误的构造方法**：直接给每个人都安排一段单独的连续时间交题离场。

例如：

+ 小 A 通过 $6$ 题，WA 了 $10$ 次；小 B 通过 $6$ 题，WA 了 $9$ 次；$x=1$；按照上面的方法构造。

+ 小 A 在第 $6$ 分钟通过了 $6$ 题，总罚时数 $10x+6=16$​。
+ 小 B 在第 $12$ 分钟通过了 $6$ 题，总罚时数 $9x+12=21$​。
+ $21>16$，小 B 没有「爆切比赛」。

【所以】我们需要让先离场的罚时尽可能大，后离场的罚时尽可能小，

【所以】**先离场的后开始交，后离场的先开始交。**

听着有点绕，请看正确的构造方法。

**正确的构造方法**：

+ WA **少**的**先**提交，WA **多**的**后**提交，
+ 每个人**留一题**到最后，WA **多**的**先**交完最后一题离场，WA **少**的**后**提交最后一题再离场。

这样就可以保证前面离场的罚时大于后面离场的。

## $\large\mathfrak{3rd.\ Code|}$ 代码

排序方式清奇请见谅（因为懒得写结构体（雾。

```cpp
#include <bits/stdc++.h>
#define ll long long
#define FILE(x) freopen(x ".in", "r", stdin), freopen(x ".out", "w", stdout);
using namespace std;
const int N = 1e5 + 10, M = 3e5 + 10;
int n, m, x, a[N], k[N], p[N], s[M], t[M], cnt;
inline bool cmp(int x, int y) {
    if (a[x] == a[y]) return k[x] > k[y];
    return a[x] < a[y];
}
int main() {
    // clock_t Start_Time=clock();
    // ios::sync_with_stdio(false);
    // cin.tie(0),cout.tie(0);
    cin >> n >> m >> x;
    cout << n << endl;
    for (int i = 1; i <= n; p[i] = i, i ++) cin >> a[i];
    for (int i = 1; i <= n; i ++) cin >> k[i];
    sort(p + 1, p + n + 1, cmp);
    for (int i = 1; i <= n; i ++) {
        int q = i;
        for (; q <= n && a[p[q]] == a[p[q + 1]]; q ++) ;
        for (; q >= i; q --) 
            for (int j = 1; j < a[p[q]]; j++)   // 这里是小于号！因为还要留一题。
                s[++ cnt] = p[q];			    // 后离场先开做。
        for (; i <= n && a[p[i]] == a[p[i + 1]]; i ++) 
            s[++ cnt] = p[i], t[cnt] = k[p[i]]; // 留一题最后交。
        s[++ cnt] = p[i], t[cnt] = k[p[i]];
    }
    for (int i = 1; i <= cnt; i ++) cout << s[i] << " ";
    cout << endl;
    for (int i = 1; i <= cnt; i ++) cout << t[i] << " ";
    // cout<<"\n- - - - - - - - - - - - - - - - - - - - - - - - - - - - - -\nRuntime: "<<clock()-Start_Time<<" ms\n";
    // system("pause");
    return 0;
}
```

