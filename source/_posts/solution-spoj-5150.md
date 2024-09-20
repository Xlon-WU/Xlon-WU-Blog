---
title: 【知识】并查集的单点删除 &【题解】SP5150
date: 2024-9-23-20:30
tags: 
    - 知识
    - 题解
    - 并查集
    - 原创
banner: https://api.xsot.cn/bing?jump=true
mathjax: true
excerpt: "并查集的单点删除作为一个很冷门的知识，很多地方讲得或许不是很好，本文将带你学会并查集的单点删除。"
---

## $\mathfrak{1st.}$ 前言

[-->题目传送门<--](https://www.luogu.com.cn/problem/SP5150)

这篇题解已在洛谷上通过了。

原先这道题的难度是紫，我觉得题目翻译看不懂，就去讨论版[发了个贴](https://www.luogu.com.cn/discuss/820298)，然后题管更新了题目翻译，并且把难度降到了蓝……

## $\mathfrak{2nd.}$ 思路

赶时间或嫌啰嗦的可以直接跳到『思路归纳』。

### 我们先从普通并查集开始思考

对于删除单点 $x$，我们想到的第一个方法肯定是把 $x$ 的父亲指向自己。

**但是这样会把 $x$ 的下属也跟着 $x$ 孤立出来**，这明显不是我们想要的，于是第一个问题产生了。

### 如何解决问题 1

既然直接改变 $x$ 点的父亲行不通，那我们就干脆直接把原先的 $x$ **留在那儿**，成为一个**虚点**。

然后我们再**建立一个新的点**成为真正的 $x$ 点，这样 $x$ 的下属找祖宗的时候就可以正常经过虚点，但我们实际的 $x$ 却在其他地方。

说起来很复杂，但代码就一句：

```cpp
inline void Del(ll x){
    f[x]=dex++;
}
```

至此，问题 1 解决了。

但是这里又产生了第二个问题：**我怎么才能知道真正的 $x$ 点在哪？**

### 如何解决问题 2

这个简单，直接用 $f_{0\sim n-1}$ 存真正的 $x$ 点在 $f$ 数组中的下标（注意这里的点是从 $0$ 开始编号的）。

至于初始点，我们就用 $f_{n\sim 2n-1}$ 存。

### 思路归纳

1. $f_{0\sim n-1}$ 存真正的点的下标；

2. $f_{n\sim 2n-1}$ 存初始点；

3. $f_{2n}$ 开始往后 存新建的点；

4. 删除（分离）点 $x$ 时，新建一个点来存真的 $x$，以前的则成为虚点供其下属找祖宗；

5. 查询和合并正常写就行了。

6. 统计并查集数量不用多说了吧。

最坏情况每个操作都新建一个点，故 $f$ 数组应开 $N+M$ 最大值。

## $\mathfrak{3rd.}$ 代码实现

稍微压行，不喜轻喷。

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;
const ll N=2e5+10,Q=2e6+10;
ll n,q,f[N*2+Q],dex;
bool book[N*2+Q];
ll Findfa(ll x){
    if(x==f[x])return x;
    return f[x]=Findfa(f[x]);
}
inline void Union(ll x,ll y){
    ll u=Findfa(x),v=Findfa(y);
    if(u==v)return;
    f[u]=v;
}
inline void Del(ll x){
    f[x]=dex++;
    //新建点
}
int main(){
    //数据挺大的，要关同步流
	ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    for(ll T=1;;T++){
        cin>>n>>q;
        if(n==0&&q==0)return 0;
        dex=n;
        //指向初始点
        for(ll i=0;i<n;i++)f[i]=dex++;
        for(ll i=n;i<n*2+q+10;i++)f[i]=i;
        while(q--){
            ll x,y;
            char c;
            cin>>c;
            if(c=='M'){
                cin>>x>>y;
                Union(x,y);
            }else{
                cin>>x;
                Del(x);
            }
        }
        ll ans=0;
        memset(book,0,sizeof(book));
        for(ll i=0;i<n;i++)f[i]=Findfa(i);
        cout<<endl;
        for(ll i=0;i<n;i++){
            if(!book[f[i]])ans++;
            book[f[i]]=1;
        }
        cout<<"Case #"<<T<<": "<<ans<<endl;
    }
    //没错，就这些了，和普通并查集相比就多了个删除函数，还有……就没区别了。
    return 0;
}
```