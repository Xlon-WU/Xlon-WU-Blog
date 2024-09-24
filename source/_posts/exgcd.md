---
title: 【知识】 扩展欧几里得算法
date: 2024-09-24 14:55:43
categories: 
    - 数论
tags: 
    - 数论
    - 原创
mathjax: true
banner: https://api.xsot.cn/bing?jump=true
excerpt: "扩展欧几里得算法是一个常用的数论算法，可以用于求解不定方程、线性同余方程、模意义下的乘法逆元等。网上很多资料对于代码的关键部分证明十分模糊，这篇文章带你完全理解扩展欧几里得算法。"
---

## $\large\mathfrak{1st.}$ 前言

扩展欧几里得算法是一个常用的数论算法，可以用于求解不定方程、线性同余方程、模意义下的乘法逆元等。网上很多资料对于代码的关键部分证明十分模糊，这篇文章带你完全理解扩展欧几里得算法。

## $\large\mathfrak{2nd.}$​ 回顾普通欧几里得算法

大部分人初学算法肯定会接触到求最大公约数的欧几里得算法，它也叫做 「辗转相除法」。

它的求解过程十分简单：

```cpp
int gcd(int a, int b) {
    if (b == 0) return a;         // 余数为 0，除尽了
    else return gcd(b, a % b);    // 大数模去小数，递归计算
}
```

原理也可以用小学二年级知识理解：假设 $a,b$（$a>b$）的最大公因数是一个苹果，则 $a,b$ 肯定都是若干个苹果，则 $a\ \text{mod}\ b$ 也肯定是若干个苹果，故 $b$ 和 $a\ \text{mod}\ b$ 的最大公因数也是一个苹果，然后按照前面的方法 「辗转相除」，最后 $b$ 为 $0$ 时就说明上一步模出来的余数为 $0$，这时这一步的 $a$（也就是上一步的 $b$）一个苹果啦。

刚才的过程中我们用到了欧几里得定理，即 $\gcd(a,b)=\gcd(b,a\ \text{mod}\ b)$，不过证明过程简化了。

由此我们还可以得出每一层递归时的 $\gcd(a,b)$ 都是一样的（废话。

{% notel blue fa-book 历史资料 %}
欧几里得算法，又称辗转相除法，被认为是世界上最早的算法（公元前 $300$ 年）。辗转相除法首次出现于欧几里得的 《几何原本》 第 Ⅶ 卷，命题 Ⅰ 和 Ⅱ。

在中国，东汉出现的 《九章算术》 中介绍了一种与欧几里得算法的弱化版 「更相减损法」，其实就是把取模换成了一点一点地减。
{% endnotel %}

## $\large\mathfrak{3rd.}$ 扩展欧几里得算法

首先我们需要知道 **「裴蜀定理」**：对于任意 $a,b\in\mathbb{Z}^*$ 都存在 $x,y\in\mathbb{Z}$ 满足 $ax+by=\gcd(a,b)$（注意这里的 $x,y$ 可以为负）。

裴蜀定理的一个推论是，不定方程 $ax+by=n$ 若存在合法解，则必定会有 $\gcd(a,b)|n$（可以理解为把 $x,y$ 都翻了几倍），也就是说 $n$ 最小为 $\gcd(a,b)$。

于是我们可以通过这种方法来判断不定方程 $ax+by=n$ 是否有解。

但人类仍不知足……我还想知道在 $n=\gcd(a,b)$ 的情况下，解到底是多少（把这个解翻个若干倍就是其他解了，因为 $n$ 一定是 $\gcd(a,b)$ 的倍数。

**注意**：扩展欧几里得算法算法只能解 $ax+by=\gcd(a,b)$ 的不定方程。

它的实现方法是在普通欧几里得算法递归求 $\gcd(a,b)$ **回溯的时候**顺带把 $x,y$ 算出来（因为要先求 $\gcd(a,b)$）。

计算 $x,y$ 的过程：我们在回溯到某一层的算出的 $x,y$ 都是关于当前层的 $a,b$ 满足 $ax+by=\gcd$（每一层的 $\gcd$ 都一样）。

详细计算方法请看下面代码详解（保证对每一句话都做详细解释）：

```cpp
int exgcd(int a, int b, int &x, int &y) {  // 这里x和y都要被修改，所以使用引用参数
    if (!b) {							// b等于0，到达递归出口
        x = 1, y = 0;					// 此时的a为gcd，x=1,y=0可满足条件
        return a;
    }
    int res = exgcd(b, a % b, x, y);	// 存下gcd
    int t = x;							// 将下一层的x暂存下来
    x = y, y = t - a / b * y;			// 这个请看下面解释
    return res;
}
```

其实我们可以发现，如果我们只要求 $x,y$ 的话返回 $\gcd(a,b)$ 没有任何用，可以直接写成 `void` 类型。

对于 `x = y, y = t - a / b * y;` 这个计算，我第一次看到的时候也觉得十分神秘，为什么这样推出来的 $x,y$ 可以满足条件呢？

**重点来了！！！**接下来的证明网上很多自称 「超详解」 的教程都没讲或糊弄一下就过去了。过程并不复杂，大概只有初中知识，长点脑子的应该都能看懂。

设当前层要求的 $x,y$ 分别为 $x_1,y_1$，下一层已经求好了的 $x,y$ 分别为 $x_2,y_2$（回溯时从下往上推）。

则 $ax_1+by_1=\gcd(a,b),\ bx_2+(a\ \text{mod}\ b)y_2=\gcd(b,a\ \text{mod}\ b),$

由欧几里得定理可知：$\gcd(a,b)=\gcd(b,a\ \text{mod}\ b),$

故 $ax_1+by_1=bx_2+(a\ \text{mod}\ b)y_2,\qquad(1)$

又因为 $a\ \text{mod}\ b=a-\left\lfloor\cfrac{a}{b}\right\rfloor\cdot b,$

将其代入 $(1)$ 式得 $ax_1+by_1=bx_2+y_2\left(a-\left\lfloor\cfrac{a}{b}\right\rfloor\cdot b\right),$

展开得 $ax_1+by_1=bx_2+ay_2+by_2\cdot\left\lfloor\cfrac{a}{b}\right\rfloor=ay_2+b\left(x_2-y_2\cdot\left\lfloor\cfrac{a}{b}\right\rfloor\right),$

即 $ax_1+by_1=ay_2+b\left(x_2-y_2\cdot\left\lfloor\cfrac{a}{b}\right\rfloor\right),$

又因为 $a=a,b=b$，

且注意到 $x_1$ 与 $y_2$ 对应，$y_1$ 与 $x_2-y_2\cdot\left\lfloor\cfrac{a}{b}\right\rfloor$​ 对应，

故 $x_1=y_2,y_1=x_2-y_2\cdot\left\lfloor\cfrac{a}{b}\right\rfloor$。

**以上证明过程来自 OI Wiki [扩展欧几里得算法](https://oi-wiki.org/math/number-theory/gcd/#%E6%89%A9%E5%B1%95%E6%AC%A7%E5%87%A0%E9%87%8C%E5%BE%97%E7%AE%97%E6%B3%95)，稍有改动。**

然后我们的 `x = y, y = t - a / b * y;` 就出来啦！是不是觉得很神奇！

最后我们要求不定方程 $ax+by=\gcd(a,b)$ 的解时只需要这样调用：

```cpp
int x, y;					// 因为是引用参数，所以x和y需要单独开变量
int d = gcd(a, b, x, y);	// 此时x和y里就是对应的解，d就是a和b的最大公因数
```

