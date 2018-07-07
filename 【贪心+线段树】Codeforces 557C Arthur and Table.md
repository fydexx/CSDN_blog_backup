# 【贪心+线段树】Codeforces 557C Arthur and Table

[题面在这里](http://codeforces.com/problemset/problem/557/C)



其实很简单……

暴枚最长桌脚的长度$l$，然后长度比$l$长的桌脚全部都要砍掉

长度比$l$短的桌脚选择代价前$k$小的砍掉

用线段树维护……



示例程序 ：

```C++
#include<cstdio>
#include<vector>
#include<algorithm>
using namespace std;
typedef long long ll;
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

const int maxn=100005;
int n,m,l[maxn];
ll ans=1ll<<62,sum=0;
vector<int> a[maxn];
struct node{
	node *l,*r;
	int L,R,s;ll w;
	node () {}
	node (int _L,int _R):L(_L),R(_R),s(0),w(0) {}
	void pushup() {s=l->s+r->s; w=l->w+r->w;}
}nil,bace[maxn<<1];
typedef node* P_node;
P_node null,len,Rot;
void init(){
	nil=node(0,0);null=&nil;
	null->l=null->r=null;len=bace;
}
P_node newnode(int L,int R){
	*len=node(L,R);
	len->l=len->r=null;
	return len++;
}
P_node build(int L,int R){
	P_node x=newnode(L,R);
	if (L==R) return x;
	int mid=L+R>>1;
	x->l=build(L,mid); x->r=build(mid+1,R);
	return x;
}
void insert(P_node x,int k,int wi){
	if (x->L==x->R) {x->s+=wi;x->w=(ll)x->s*x->L;return;}
	int mid=x->L+x->R>>1;
	if (k<=mid) insert(x->l,k,wi);else insert(x->r,k,wi);
	x->pushup();
}
ll query(P_node x,int k){
	if (k> x->s) return 1ll<<62;
	if (x->L==x->R) return (ll)x->L*k;
	if (k<= x->l->s) return query(x->l,k);
	return query(x->r,k- x->l->s)+ x->l->w;
}
int main(){
	n=red();m=100000;
	for (int i=1;i<=n;i++) l[i]=red();
	for (int i=1,x;i<=n;i++) x=red(),sum+=x,a[l[i]].push_back(x);
	for (int i=1;i<=m;i++){
		int Max=0,cd=0;
		for (int j : a[i])
		 if (j>=Max) cd=Max,Max=j;else
		 if (j>cd) cd=j;
		if (Max) ans=min(ans,sum-Max);
		if (cd) ans=min(ans,sum-Max-cd);
	}
	init();Rot=build(1,m);
	for (int i=1,all=0;i<=m;i++){
		for (int j: a[i]) sum-=j,all++;
		if (all==a[i].size()&&a[i].size()>2) ans=min(ans,sum);
		if (a[i].size()>1&&all>=a[i].size()*2-1) ans=min(ans,sum+query(Rot,all-a[i].size()*2+1)) ;
		for (int j: a[i]) insert(Rot,j,1);
	}
	printf("%I64d",ans);
	return 0; 
}
```