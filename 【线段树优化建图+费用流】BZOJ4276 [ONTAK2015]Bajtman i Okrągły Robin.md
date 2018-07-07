[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=4276)

很显然是网络流。
可以用流量作为使其符合题意的限制条件
那么就是刷最大费用最大流

经过上面的分析，很快就有了建图方案：

 1. 源点到所有贼建边，容量1，费用ci
 2. 每个贼向能作案的时间点建边，容量1，费用0
 3. 每个时间点向汇点建边，容量1，费用0

边数有点多，那么就线段树优化建图即可

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
#define nc getchar
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res*f;
}

const int maxn=15005,maxe=180005,INF=0x3f3f3f3f;
int n,S,T;
int tot=1,son[maxe],nxt[maxe],lnk[maxn],cap[maxe],flw[maxe],w[maxe];
void add(int x,int y,int z,int wi){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;cap[tot]=z;flw[tot]=0;w[tot]=wi;
	son[++tot]=x;nxt[tot]=lnk[y];lnk[y]=tot;cap[tot]=0;flw[tot]=0;w[tot]=-wi;
}
struct node{
	node *l,*r;
	int L,R,id;
}base[10005],nil;
typedef node* P_node;
P_node null,Rot,len;
void init(){
	null=&nil;null->l=null->r=null;
	len=base+1;
}
P_node newnode(int L,int R){
	len->L=L;len->R=R;len->id=len-base;
	len->l=len->r=null;
	return len++;
}
P_node build(int L,int R){
	P_node x=newnode(L,R);
	if (L==R) return x;
	int mid=L+R>>1;
	x->l=build(L,mid),x->r=build(mid+1,R);
	add(x->id,x->l->id,INF,0);add(x->id,x->r->id,INF,0);
	return x;
}
void find(P_node x,int L,int R,int *s){
	if (L<=x->L&&x->R<=R) {s[++s[0]]=x->id;return;}
	if (x->R<L||R<x->L) return;
	find(x->l,L,R,s);find(x->r,L,R,s);
}
int a[maxn];
int que[maxn],dst[maxn],fa[maxn],ed[maxn];
bool vis[maxn];
bool spfa(){
	memset(vis,0,sizeof(vis));
	memset(dst,192,sizeof(dst));int temp=dst[0];
	int hed=0,til=1;
	que[1]=S;dst[S]=0;fa[S]=0;
	while (hed!=til){
		int x=que[hed=(hed+1)%maxn];
		vis[x]=0;
		for (int j=lnk[x];j;j=nxt[j])
		 if (cap[j]>flw[j]&&dst[son[j]]<dst[x]+w[j]){
		 	dst[son[j]]=dst[x]+w[j];
		 	fa[son[j]]=x,ed[son[j]]=j;
		 	if (!vis[son[j]])
		 	 vis[son[j]]=1,que[til=(til+1)%maxn]=son[j];
		 }
	}
	return dst[T]!=temp;
}
int Max_flow(){
	int res=0;
	while (spfa()){
		int Min=INF;res+=dst[T];
		for (int j=T;j!=S;j=fa[j]) Min=min(Min,cap[ed[j]]-flw[ed[j]]);
		for (int j=T;j!=S;j=fa[j]) flw[ed[j]]+=Min,flw[ed[j]^1]-=Min;
	}
	return res;
}
int main(){
	init();Rot=build(1,5000);
	n=red();int N=len-base;
	S=++N;T=++N;
	for (int i=1;i<=5000;i++)
	 a[0]=0,find(Rot,i,i,a),add(a[1],T,1,0);
	for (int i=1;i<=n;i++){
		int l=red(),r=red()-1,c=red();
		a[0]=0;find(Rot,l,r,a);add(S,++N,1,c);
		for (int i=1;i<=a[0];i++) add(N,a[i],1,0);
	}
	printf("%d",Max_flow());
	return 0;
} 
```