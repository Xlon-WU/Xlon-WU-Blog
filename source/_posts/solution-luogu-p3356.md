---
title: 【题解】 P3365 火星探险问题
date: 2024-08-27 12:00:00
tags: 
    - 题解
    - 网络流
    - 费用流
mathjax: true
banner: /img/solution-luogu-p3365-banner.png
---

## $\large\mathfrak{1st.\ Preamble|}$​ 前言

这都什么年代了网络流 24 题居然还能写题解！

个人认为这篇题解讲的还是比较详细的。

## $\large\mathfrak{2nd.\ Solution|}$ 题解

看到题目的第一眼，我的反应是这样的：这不跟[深海机器人问题](https://www.luogu.com.cn/problem/P4012)差不多吗？`Ctrl-C` `Ctrl-V` 秒了。

不过我还是讲讲怎么建模吧。

这里我定义点 $(x,y)$ 表示第 $x$ 行第 $y$ 列的数，和题目中的是反过来的 ~~（题目里的那种表示方法有点反人类）~~。

### 建模

把一个点 $(x,y)$ 拆成 $(x,y)_1$ 和 $(x,y)_2$ 两个点，$x_1$ 为入点，$x_2$ 为出点。

#### 平坦无障碍（$0$）

1. 建边 $(x,y)_1 \rightarrow (x,y)_2$，容量为 $\inf$，费用为 $0$，代表这个点可以走无数次，没有收益。
2. 建边 $(x,y)_2 \rightarrow (x+1,y)_1$，容量为 $\inf$，费用为 $0$，代表这个从这个点向南到下面那个点可以走无数次。
3. 建边 $(x,y)_2 \rightarrow (x,y+1)_1$，容量为 $\inf$，费用为 $0$，代表这个从这个点向东到右边那个点可以走无数次。

#### 障碍（$1$）

没法走，不用管它。

#### 石块（$2$）

和平坦无障碍的相比，只需要再多加一条边 $(x,y)_1 \rightarrow (x,y)_2$，容量为 $1$，费用为 $1$，代表只可以捡一次石块，收益为 $1$。

最后跑最大费用最大流即可。

### 输出

我建完模就直接输出费用，然后开开心心地跑去测样例，丫的，居然要输出路径！我当场懵逼，冥（cha）思（kan）苦（ti）想（jie）后得到了下面的方法：

对于每条路径，从起点开始搜索，每搜到一个点，选一条反向边有剩余容量（说明被走过）的临边走过去，并把反向边的剩余容量减去 $1$，直到走到终点。

如果看不懂可以看代码：

```cpp
void put_ans(int a){
    int x=1,y=1;    // 从起点开始搜索
    while(1){
        if(x>=q&&y>=p)return;  // 到达终点
        int u=B(x,y);
        for(int i=head[u];i;i=e[i].pre){
            int v=e[i].to;
            if(!e[i^1].w)continue;  // 反向边有剩余容量
            if(v==A(x+1,y)){x++,e[i^1].w--,printf("%d 0\n",a);break;}  // 向南走
            if(v==A(x,y+1)){y++,e[i^1].w--,printf("%d 1\n",a);break;}  // 向东走
        }
    }
}
```

## $\large\mathfrak{3rd.\ Code|}$ 代码

我是把边权设为负数跑最小费用最大流的方式求解最大费用最大流。代码压行请见谅。

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;
const ll inf=1e18,N=5e3+10,M=5e4+10;
inline int read(){
	int x=0,f=1;char ch=getchar();
	while (ch<'0'||ch>'9'){if (ch=='-') f=-1;ch=getchar();}
	while (ch>='0'&&ch<='9'){x=x*10+ch-48;ch=getchar();}
	return x*f;
}
ll e_cnt=1,head[N];
struct EDGE{ll from,to,w,c,pre;}e[M<<1];
ll n,p,q,s,t,ans,cost;
ll dis[N],pre[N];
inline void add(ll from,ll to,ll w,ll c){
    e[++e_cnt]=(EDGE){from,to,w,c,head[from]};
    head[from]=e_cnt;
}
inline void add_edge(ll u,ll v,ll w,ll c){
    add(u,v,w,c),add(v,u,0,-c);
}
inline int A(int x,int y){return (x-1)*p+y;}    // (x,y)_1
inline int B(int x,int y){return A(x,y)+p*q;}   // (x,y)_2
//------------------最大费用最大流板子---------------------
bool spfa(ll s,ll t){
    bool inq[N];
    memset(pre,-1,sizeof(pre));
    for(int i=1;i<=t;i++)dis[i]=inf,inq[i]=0;
    queue<ll>que;
    dis[s]=0,inq[s]=1,que.push(s);
    while(!que.empty()){
        ll u=que.front();
        que.pop();
        inq[u]=0;
        for(int i=head[u];i;i=e[i].pre){
            if(e[i].w>0){
                ll v=e[i].to,c=e[i].c;
                if(dis[u]+c<dis[v]){
                    dis[v]=dis[u]+c;
                    pre[v]=i;
                    if(!inq[v]){
                        que.push(v);
                        inq[v]=1;
                    }
                }
            }
        }
    }
    return dis[t]!=inf;
}
void mincost(ll s,ll t){
    while(spfa(s,t)){
        ll v=t,flow=inf;
        while(pre[v]!=-1){
            ll i=pre[v],u=e[i].from;
            flow=min(flow,e[i].w);
            v=u;
        }
        v=t;
        while(pre[v]!=-1){
            ll i=pre[v],u=e[i].from;
            e[i].w-=flow;
            e[i^1].w+=flow;
            v=u;
        }
        cost+=dis[t]*flow;
        ans+=flow;
    }
}
//------------end of 最大费用最大流-------------
void put_ans(int a){
    int x=1,y=1;    // 从起点开始搜索
    while(1){
        if(x>=q&&y>=p)return;  // 到达终点
        int u=B(x,y);
        for(int i=head[u];i;i=e[i].pre){
            int v=e[i].to;
            if(!e[i^1].w)continue;  // 反向边有剩余容量
            if(v==A(x+1,y)){x++,e[i^1].w--,printf("%d 0\n",a);break;}  // 向南走
            if(v==A(x,y+1)){y++,e[i^1].w--,printf("%d 1\n",a);break;}  // 向东走
        }
    }
}
int main(){
    n=read(),p=read(),q=read();
    s=p*q*2+1,t=s+1;
    for(int i=1;i<=q;i++){
        for(int j=1;j<=p;j++){
            int x;
            x=read();
            if(x!=1){    // 0和2有三条相同边，合在一起，1啥也不干
                add_edge(A(i,j),B(i,j),inf,0);
                if(i+1<=q)add_edge(B(i,j),A(i+1,j),inf,0);
                if(j+1<=p)add_edge(B(i,j),A(i,j+1),inf,0);
                if(x==2)add_edge(A(i,j),B(i,j),1,-1);  // 2多建一条边
            }
        }
    }
    add_edge(s,A(1,1),n,0);
    add_edge(B(q,p),t,n,0);
    mincost(s,t);
    for(int i=1;i<=ans;i++)put_ans(i);
    return 0;
}
```

[AC 记录](https://www.luogu.com.cn/record/172049267)

## $\large\mathfrak{4th.\ Postscript|}$ 后记

第一遍编号标错居然可以拿 $84$ 分（只有 #5 没过），这数据着实有点水……