---
title: 【题解】 AT_arc073_d Many Moves
date: 2024-09-02 21:00:00
tags: 
    - 题解
    - 线段树
    - 动态规划
mathjax: true
banner: https://api.xsot.cn/bing?jump=true
---

### 未完待续……

## $\large\mathfrak{1st.\ Preamble|}$ 前言

[洛谷题目传送门](https://www.luogu.com.cn/problem/AT_arc073_d) or [AtCoder 原题传送门](https://atcoder.jp/contests/arc073/tasks/arc073_d)

在某次膜你赛里见到了这道题，赛场上写了个假 DP，喜提零分，然后学长的题解写错了几个字，加上脑抽，瞪眼看了俩小时没看出来，明白后感觉我就是个智 X。

如果其他题解看不懂的可以尝试看看这篇（也就可能只有我这个智 X 需要）。

## $\large\mathfrak{2nd.\ Solution|}$ 题解

首先我们知道每次操作最多移动一个棋子（废话）。

然后我们记 $f_{i,a,b}$ 表示操作 $i$ 次，两颗棋子分别在 $a,b$ 所消耗的最小时间。由于 $a,b$ 两者中至少有一个是 $x_i$（根据题目要求），故我们可以少记一维，用 $f_{i,a}$ 表示一个在 $x_i$，另一个在 $a$​ 的最小耗时。

于是我们有转移：

$$
f_{i,j}=f_{i-1,j}+|x_i-x_{i-1}|\\
f_{i,x_i}=\min_{j=1}^{V}\{f_{i-1,j}+|x_i-j|\}
$$

第一个柿子表示原先在 $j$ 的棋子不动，原先在 $x_{i-1}$ 的棋子移动到 $x_i$，需要 $|x_i-x_{i-1}|$ 的代价。

第二个柿子表示原先在 $x_i$ 的棋子不动，原先在 $j$ 的棋子移动到 $x_i$，需要 $|x_i-j|$ 的代价。

然后我们可以发现，$f_i$ 这一层中的值只依赖于 $f_{i-1}$，然后我们就可以滚动数组优化，变成了一维（三维压成一维，是不是很有成就感）。

由于第一种转移的代价是个定值，所以可以直接区间加。

第二种转移我们可以先拆绝对值，

**未完待续……**

## $\large\mathfrak{3rd.\ Code|}$ 代码

**未完待续……**

## $\large\mathfrak{4th.\ Postscript|}$ 后记

**未完待续……**
