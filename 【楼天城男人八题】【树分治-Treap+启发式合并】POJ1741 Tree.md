[题面在这里](http://poj.org/problem?id=1741)

待我先膜拜一下楼教主……

首先这题是很明显的树分治
~~想说点什么却发现已经没什么好说了~~

-----------------------
然后我们来看另一种解法：平衡树乱搞
这里用的是Treap实现

对于每个节点，用Treap记录该子树每个节点到根(默认为1)的距离
那么如何统计答案？
对于兄弟子树，他们之中的任意节点间都可统计方案数，而且不会重复
其实统计方案数就是询问某Treap中key比x小的个数
统计两棵Treap之间的方案数就是把其中一棵树拆成一个一个点，不断询问
统计之后，将兄弟子树合并，得到了父亲子树的Treap
最后统计一棵子树的根到每个点是否能对答案做出贡献，并插入根节点 
这里就要讲一下启发式合并，在合并过程中，如果我们每次都把较小的Treap拆成一个个点并插入另一个Treap中
就可以保证每个点最多被插入$logN$次，那么复杂度就是$Nlog^2N$（注意空间也要开$NlogN$） 

示例程序：
树分治：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=10005,maxe=2*maxn;
int n,k,hvy,S,ans;
int tot,lnk[maxn],nxt[maxe],son[maxe],w[maxe];
int siz[maxn],now[maxn],dep[maxn],MAX[maxn];
bool vis[maxn];
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
void add(int x,int y,int z){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;w[tot]=z;
}
void get_hvy(int x,int fa){
	siz[x]=1;MAX[x]=0;
	for (int j=lnk[x];j;j=nxt[j])
	 if (son[j]!=fa&&!vis[son[j]]){
	 	get_hvy(son[j],x);
	 	siz[x]+=siz[son[j]];
	 	MAX[x]=max(MAX[x],siz[son[j]]);
	 }
	MAX[x]=max(MAX[x],S-siz[x]);
	if (!hvy||MAX[x]<MAX[hvy]) hvy=x;
}
void get_dep(int x,int fa){
	now[++now[0]]=dep[x];
	for (int j=lnk[x];j;j=nxt[j])
	 if (fa!=son[j]&&!vis[son[j]]){
		dep[son[j]]=dep[x]+w[j];
		get_dep(son[j],x);
	}
}
int get_sum(int x,int dst){
	now[0]=0;int res=0;
	dep[x]=dst;get_dep(x,0);
	sort(now+1,now+1+now[0]);
	for (int i=1,j=now[0];i<=now[0];i++){
		while (j>i&&now[i]+now[j]>k) j--;
		res+=max(0,j-i);
	}
	return res;
}
void get_ans(int x){
	vis[x]=1;ans+=get_sum(x,0);
	for (int j=lnk[x];j;j=nxt[j])
	 if (!vis[son[j]]){
		ans-=get_sum(son[j],w[j]);
		hvy=0;S=siz[son[j]];get_hvy(son[j],0);
		get_ans(hvy);
	 }
}
int main(){
	for (n=red(),k=red();n||k;n=red(),k=red()){
		memset(vis,0,sizeof(vis));
		memset(lnk,0,sizeof(lnk));tot=0;
		for (int i=1,x,y,z;i<n;i++)
		 x=red(),y=red(),z=red(),add(x,y,z),add(y,x,z);
		hvy=0;S=n;get_hvy(1,0);
		ans=0;get_ans(hvy);
		printf("%d\n",ans);
	}
	return 0;
}
```

Treap：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=10005,maxe=20005;
int n,K,ans;
int son[maxe],nxt[maxe],lnk[maxn],w[maxe],tot;
bool vis[maxn];
void add(int x,int y,int wi){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;w[tot]=wi;
}
inline int red(){
	int tot=0;char ch=getchar();
	while (ch<'0'||'9'<ch) ch=getchar();
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot;
}
struct node{
	node* s[2];
	int size,k,cnt,f;
	void maintain() {size=s[0]->size+s[1]->size+cnt;}
}treap[132880],nil;
typedef node* P_node;
P_node null,len,rt[maxn];
void clear(){
	null=&nil;
	null->s[0]=null->s[1]=null;
	null->size=null->cnt=0;
	len=treap;
}
P_node newnode(int key,int num){
	len->k=key;len->s[0]=len->s[1]=null;
	len->cnt=len->size=num;len->f=rand();
	return len++;
}
void rot(P_node &x,int d){
	P_node k=x->s[d^1];x->s[d^1]=k->s[d];k->s[d]=x;
	x->maintain();k->maintain();x=k;
}
void ist(P_node &x,int key,int num){
	if (x==null) x=newnode(key,num);else
	if (key==x->k) x->cnt+=num;else{
		int d=key>x->k;
		ist(x->s[d],key,num);if (x->s[d]->f > x->f) rot(x,d^1);
	}
	x->maintain();
}
void merge(P_node &a,P_node b){
	if (b==null) return;
	ist(a,b->k,b->cnt);
	merge(a,b->s[0]);merge(a,b->s[1]);
}
int Rank(P_node x,int key){
	if (x==null) return 0;
	if (key==x->k) return x->s[0]->size+x->cnt;
	if (key<x->k) return Rank(x->s[0],key);
	return Rank(x->s[1],key)+x->s[0]->size+x->cnt;
}
int asksum(P_node a,P_node b,int x){
	if (b==null) return 0;
	return b->cnt*Rank(a,x-b->k)+asksum(a,b->s[0],x)+asksum(a,b->s[1],x);
}
void dfs(int x,int dst){
	vis[x]=1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!vis[son[j]]){
	 	dfs(son[j],dst+w[j]);
		if (rt[son[j]]->size > rt[x]->size) swap(rt[son[j]],rt[x]);
	 	ans+=asksum(rt[x],rt[son[j]],2*dst+K);merge(rt[x],rt[son[j]]);
	 }
	ans+=Rank(rt[x],dst+K);
	ist(rt[x],dst,1);
}
int main(){
	freopen("test.in","r",stdin);
	freopen("test.out","w",stdout);
	n=red(),K=red();
	while (n){
		memset(lnk,0,sizeof(lnk));tot=0;
		clear();for (int i=1;i<=n;i++) rt[i]=null;
		for (int i=1,x,y,wi;i<n;i++) x=red(),y=red(),wi=red(),add(x,y,wi),add(y,x,wi);
		memset(vis,0,sizeof(vis));ans=0;
		dfs(1,0);
		printf("%d\n",ans);
		n=red(),K=red();
	}
}
```