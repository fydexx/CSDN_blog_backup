[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3073)

此题题意很简单，但是会发现直接暴力建边的话，边数会非常多
所以我们就需要一个新姿势：线段树优化建图

暴力建图有$n^2m$条边
考虑一个较为高效的方法：
对于[a,b]向[c,d]连单向边这个子问题，可以新建辅助节点p
[a,b]的所有点向p连边，边权为1，p向[c,d]所有点连边，边权为0
这样边数就减为$nm$级别，但还是太多

可以发现，上述优化是基于区间的线性操作
这就为我们进一步优化提供了思路：
线段树可以很好地解决区间问题，所以用线段树优化一波

具体可以这样做：
建两棵线段树A和B，分别表示出和入
![这里写图片描述](http://img.blog.csdn.net/20170723135435553?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
A的所有节点向父节点连边，B的所有节点向子节点连边
同时B的叶子节点向A的对应节点连边，边权都是0
![这里写图片描述](http://img.blog.csdn.net/20170723140149120?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
对于[a,b]向[c,d]连单向边，找到A中对应[a,b]的节点，B中对应[c,d]的节点
然后新建虚拟节点p，将这些节点按照前面的较优方法建边即可
![这里写图片描述](http://img.blog.csdn.net/20170723141642088?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

不难发现，此时A或B的叶子节点就代表原图节点
我们习惯将A的叶子节点看作原图节点，其他都是辅助节点

回到这道题，由于边权有01，所以最短路一波即可

示例程序：

```
#include<cstdio>
#include<cstring>
using namespace std;
inline char nc(){
    static char buf[100000],*p1=buf,*p2=buf;
    return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline int red(){
    int res=0,f=1;char ch=nc();
    while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
    while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
    return res*f;
}
 
const int maxn=2200005,maxe=10000005;
int n,m,S;
int tot,nxt[maxe],son[maxe],lnk[maxn],w[maxe];
inline void add(int x,int y,int z=0){// printf("%d %d %d\n",x,y,z);
    son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;w[tot]=z;
}
struct node{
    node *l,*r;
    int L,R,id; 
}base[2000005],nil;
typedef node* P_node;
P_node null,len,Ra,Rb;
void init(){
    null=&nil;null->l=null->r=null;
    len=base+1;
}
P_node newnode(int L,int R){
    len->L=L;len->R=R;len->id=len-base;
    len->l=len->r=null;
    return len++;
}
P_node build(int L,int R,bool d){
    P_node x=newnode(L,R);
    if (L==R) return x;
    int mid=L+R>>1;
    x->l=build(L,mid,d);x->r=build(mid+1,R,d);
    if (d) add(x->id,x->l->id),add(x->id,x->r->id);else
     add(x->l->id,x->id),add(x->r->id,x->id); 
    return x;
}
void find(P_node x,int L,int R,int *s){
    if (L<=x->L&&x->R<=R) {s[++s[0]]=x->id;return;}
    if (x->R<L||R<x->L) return;
    find(x->l,L,R,s);find(x->r,L,R,s);
}
int a[maxn],id[maxn];
int que[maxn],dst[maxn];
bool vis[maxe];
void spfa(){
    memset(vis,0,sizeof(vis));
    memset(dst,63,sizeof(dst));
    int hed=0,til=1;
    dst[id[S]]=0;que[1]=id[S];
    while (hed!=til){
        int x=que[hed=(hed+1)%maxn];
        vis[x]=0;
        for (int j=lnk[x];j;j=nxt[j])
         if (dst[son[j]]>dst[x]+w[j]){
            dst[son[j]]=dst[x]+w[j];
            if (!vis[son[j]])
             vis[son[j]]=1,que[til=(til+1)%maxn]=son[j];
         }
    }
}
int main(){
    n=red(),m=red();S=red();
    init();
    Ra=build(1,n,0);Rb=build(1,n,1);
    int N=len-base;
    for (int i=1;i<=n;i++)
     a[0]=0,find(Ra,i,i,a),id[i]=a[1],
     a[2]=0,find(Rb,i,i,a+2),add(a[3],a[1]);
    for (int i=1;i<=m;i++){
        int l=red(),r=red(),ll=red(),rr=red();
        a[0]=0;find(Ra,l,r,a);N++;
        for (int j=1;j<=a[0];j++) add(a[j],N,1);
        a[0]=0;find(Rb,ll,rr,a);
        for (int j=1;j<=a[0];j++) add(N,a[j],0);
         
        a[0]=0;find(Ra,ll,rr,a);N++;
        for (int j=1;j<=a[0];j++) add(a[j],N,1);
        a[0]=0;find(Rb,l,r,a);
        for (int j=1;j<=a[0];j++) add(N,a[j],0);
    }
    spfa();
    for (int i=1;i<=n;i++)
     printf("%d\n",dst[id[i]]);
    return 0;
}
﻿
```