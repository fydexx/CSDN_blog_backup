# 【DFS序+线段树】BZOJ1103 [POI2007]大都市meg

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1103)



很显然可以用DFS序把树上问题转化为区间问题

修改一条边只影响了下面那棵子树中所有点的答案

线段树维护区间加，单点询问就好了

示例程序：

```C++
#include<cstdio>
#include<algorithm>
#define Fst first
#define Sec second
#define mp make_pair
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
inline char fstchar(){
	char ch=nc();while (ch<'A'||'Z'<ch) ch=nc();
	return ch;
}

const int maxn=250005,maxe=500005;
pair<int,int> E[maxn];
int n,q,times,in[maxn],out[maxn],dep[maxn],id[maxn];
int tot,nxt[maxe],lnk[maxn],son[maxe];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
void dfs(int x){
	in[x]=++times;id[times]=x;
	for (int j=lnk[x];j;j=nxt[j])
	 if (son[j]>x){
	 	dep[son[j]]=dep[x]+1;
	 	dfs(son[j]);
	 }
	out[x]=times;
}
struct node{
	node *l,*r;
	int L,R,s,tag;
	node () {}
	node (int _L,int _R):L(_L),R(_R),s(0),tag(0) {}
	void pushup() {s=l->s+r->s;}
	void pushdown(){
		if (L==R||!tag) return;
		l->tag+=tag;l->s+=tag*(l->R - l->L +1);
		r->tag+=tag;r->s+=tag*(r->R - r->L +1);
		tag=0;
	}
}nil,base[maxe];
typedef node* P_node;
P_node null,len,Rot;
void init(){
	nil=node(0,0);null=&nil;
	null->l=null->r=null;len=base;
}
P_node newnode(int L,int R){
	*len=node(L,R);
	len->l=len->r=null;
	return len++;
}
P_node build(int L,int R){
	P_node x=newnode(L,R);
	if (L==R) {x->s=dep[id[L]];return x;}
	int mid=L+R>>1;
	x->l=build(L,mid); x->r=build(mid+1,R);
	x->pushup(); return x;
}
void insert(P_node x,int L,int R){
	if (R<x->L||x->R<L) return;
	if (L<=x->L&&x->R<=R) {x->s-=x->R - x->L +1;x->tag+=-1;return;}
	x->pushdown();
	insert(x->l,L,R);insert(x->r,L,R);
	x->pushup();
}
int query(P_node x,int k){
	x->pushdown();
	if (x->L==x->R) return x->s;
	int mid=x->L+x->R>>1;
	if (k<=mid) return query(x->l,k);else return query(x->r,k);
}
int main(){
	n=red();
	for (int i=1,x,y;i<n;i++){
		x=red(),y=red();
		if (x>y) swap(x,y);E[i]=mp(x,y);
		add(x,y);add(y,x);
	}
	sort(E+1,E+n);
	dfs(1);
	init();Rot=build(1,n);
	q=red()+n-1;
	while (q--)
	 if (fstchar()=='A'){
	 	int x=red(),y=red();
	 	int t=lower_bound(E+1,E+n,mp(x,y))->Sec;
		insert(Rot,in[t],out[t]);
	 }else printf("%d\n",query(Rot,in[red()]));
	return 0;
}
```

