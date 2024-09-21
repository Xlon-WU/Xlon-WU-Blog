---
title: 【题解】 AT_arc073_d Many Moves
date: 2024-09-02 21:00:00
tags: 
    - 动态规划
    - 线段树
    - 题解
    - 原创
mathjax: true
banner: https://api.xsot.cn/bing?jump=true
---

## $\large\mathfrak{1st.\ Preamble|}$ 前言

[洛谷题目传送门](https://www.luogu.com.cn/problem/AT_arc073_d) or [AtCoder 原题传送门](https://atcoder.jp/contests/arc073/tasks/arc073_d)

在某次膜你赛里见到了这道题，赛场上写了个假 DP，喜提零分，然后学长的题解写错了几个字，加上脑抽，瞪眼看了俩小时没看出来，明白后感觉我就是个智 X。

如果其他题解看不懂的可以尝试看看这篇（也就可能只有我这个智 X 需要）。

## $\large\mathfrak{2nd.\ Solution|}$ 题解

### 思路

首先我们知道每次操作最多移动一个棋子（废话）。

然后我们记 $f_{i,a,b}$ 表示操作 $i$ 次，两颗棋子分别在 $a,b$ 所消耗的最小时间。由于 $a,b$ 两者中至少有一个是 $x_i$（根据题目要求），故我们可以少记一维，用 $f_{i,a}$ 表示一个在 $x_i$，另一个在 $a$​ 的最小耗时。

于是我们有转移：

$$
f_{i,j}=f_{i-1,j}+|x_i-x_{i-1}|\\
f_{i,x_i}=\min_{j=1}^{V}\{f_{i-1,j}+|x_i-j|\}
$$

第一个柿子表示原先在 $j$ 的棋子不动，原先在 $x_{i-1}$ 的棋子移动到 $x_i$，需要 $|x_i-x_{i-1}|$ 的代价。

第二个柿子表示原先在 $x_i$ 的棋子不动，原先在 $j$ 的棋子移动到 $x_i$，需要 $|x_i-j|$ 的代价。

然后我们可以发现，$f_i$ 这一层中的值只依赖于 $f_{i-1}$​，然后我们就可以滚动数组优化，变成了一维（三维压成一维，是不是很有成就感）。接下来第一维全部省略。

由于第一种转移的代价是个定值，所以可以直接区间加。

第二种转移我们可以先拆绝对值，拆完后变成下面这样：
$$
f_{x_i}=\min\left\{\min_{j=1}^{x_i}\{f_j-j+x_i\},\min_{j=x_i+1}^{V}\{f_j+j-x_i\}\right\}
$$
这个柿子里的两个小柿子中都有一个固定的 $x_i$，我们只需要求区间最小 $f_j-j$ 和 $f_j+j$，这便转化为了一个动态 RMQ 问题。

**最终实现方法**：维护一棵线段树，需要支持区间加法、查询区间最小 $f_j-j$、查询区间最小 $f_j+j$ 这三个功能。每次查询按照上面的方法计算。

**时间复杂度**：$O(N\log N+Q\log N)$（建树 $O(N\log N)$；每次查询 $O(\log N)$，所有查询共 $O(Q\log N)$）。

## $\large\mathfrak{3rd.\ Code|}$ 代码

```en
#include <bits/stdc++.h>
#define ll long long
#define FOR(i, l, r) for (ll i = l; i <= r; i++)
#define FILE(x) freopen(x ".in", "r", stdin), freopen(x ".out", "w", stdout);
#define pii pair<ll, ll>
#define pll pair<long long, long long>
// #define Clock
using namespace std;
const ll N = 2e5 + 10, inf = 1e15;
ll V, a, b, m, op[N], ans;

/* -------- start of segment tree -------- */

#define lc t[x << 1]
#define rc t[x << 1 | 1]
#define ls (x << 1)
#define rs (x << 1 | 1)
#define tx t[x]

struct node {
    ll l, r, val, mi1, mi2;
} t[N << 2];

inline void pushup(ll x) {
	tx.val = min(lc.val, rc.val);
	tx.mi1 = min(lc.mi1, rc.mi1);
	tx.mi2 = min(lc.mi2, rc.mi2);
}

void build(ll x, ll l, ll r) {
    tx.l = l, tx. r = r;
	if (l == r) {
        if (l == a) {
        	tx.val = abs(op[1] - b);
        	tx.mi1 = tx.val + a;
        	tx.mi2 = tx.val - a;
        } else if(l == b) {
        	tx.val = abs(op[1] - a);
        	tx.mi1 = tx.val + b;
        	tx.mi2 = tx.val - b;
        } else tx.val = tx.mi1 = tx.mi2 = inf;
        return;
    }
    ll mid = (l + r) >> 1;
    build(ls, l, mid);
    build(rs, mid + 1, r);
    pushup(x);
}

void change(ll x, ll idx, ll now, ll w) {  // 区间加
	if (idx < tx.l || idx > tx.r) return;
	if (tx.l == idx && tx.r == idx) {
		tx.val = min(tx.val, w);
		tx.mi1 = min(tx.mi1, w + op[now - 1]);
		tx.mi2 = min(tx.mi2, w - op[now - 1]);
		return;
	}
	change(ls, idx, now, w);
	change(rs, idx, now, w);
	pushup(x);
}

ll query(ll x, ll l, ll r, ll ty){  // 这里将两个查询合并在一起
	if (r < tx.l || tx.r < l) return inf;
	if (l <= tx.l && tx.r <= r) {
		if (ty == 0) return tx.val;
		else if (ty == 1) return tx.mi1;
		else if (ty == 2) return tx.mi2;
		else return inf;
	}
	return min(query(ls, l, r, ty), query(rs, l, r, ty));
}

/* --------- end of segment tree --------- */

int main() {
#ifdef Clock
    clock_t Start_Time = clock();
#endif
    // ios::sync_with_stdio(false);
    // cin.tie(0),cout.tie(0);
    // FILE("hard");
    cin >> V >> m >> a >> b;
    for (ll i = 1; i <= m; i ++) cin >> op[i];
    build(1, 1, V);
	for (ll i = 2; i <= m; i ++){
		ans += abs(op[i] - op[i - 1]);
		ll w = min(query(1, 1, op[i], 2) + op[i], query(1, op[i], V, 1) - op[i]) - abs(op[i] - op[i - 1]);
		change(1, op[i - 1], i, w);
	}
	cout << query(1, 1, V, 0) + ans;
#ifdef Clock
    cout << "\n- - - - - - - - - - - - - - - - - - - - - - - - - - - - - -\nRuntime: " << clock() - Start_Time << " ms\n";
    system("pause");
#endif
    return 0;
}
```

## $\large\mathfrak{4th.\ Postscript|}$ 后记

![](\img\solution-at-arc073d.png)

这里其实是我脑抽了，第一次不是滚动。
