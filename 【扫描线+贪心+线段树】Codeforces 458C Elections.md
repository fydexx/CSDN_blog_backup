# 【扫描线+贪心+线段树】Codeforces 458C Elections

[题面在这里](http://codeforces.com/contest/458/problem/C)



考虑暴枚$d$表示所有的对手选票不大于$d$

那么选票大于$d$的对手都需要抢选票，如果这样自己的票数还是不够

就从选票小于等于$d$的对手哪里抢选票

这个可以用线段树维护前K小



示例程序：

```C++
#include<cstdio>
#include<vector>
#include<algorithm>
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

const int maxn=100005,maxs=10005;
int n,m,id[maxn],tic;
vector<int> a[maxn];
bool cmp(int i,int j){
	return a[i].size()>a[j].size();
}
bool dcmp(int a,int b){return a>b;}
struct node{
	node *l,*r;
	int L,R,s,w;
	node () {}
	node (int _L,int _R):L(_L),R(_R),s(0),w(0) {}
	void pushup() {s=l->s+r->s;w=l->w+r->w;}
}nil,base[maxs*2];
typedef node* P_node;
P_node null,Rot,len;
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
	if (L==R) return x;
	int mid=L+R>>1;
	x->l=build(L,mid);x->r=build(mid+1,R);
	return x;
}
void insert(P_node x,int k,int wi){
	if (x->L==x->R) {x->s+=wi;x->w+=wi*x->L;return;}
	int mid=x->L+x->R>>1;
	if (k<=mid) insert(x->l,k,wi);else insert(x->r,k,wi);
	x->pushup();
}
int query(P_node x,int k){
	if (x->L==x->R) return x->L*k;
	if (k<= x->l->s) return query(x->l,k);
	return query(x->r,k - x->l->s)+ x->l->w;
}
int main(){
	n=red(),m=0;
	init();Rot=build(0,10000);
	for (int i=1;i<=n;i++){
		int x=red(),w=red();
		a[x].push_back(w);m=max(m,x);
		if (x) insert(Rot,w,1);
	}
	tic=a[0].size();
	for (int i=1;i<=m;i++) sort(a[i].begin(),a[i].end(),dcmp),id[i]=i; 
	sort(id+1,id+1+m,cmp);
	int ans=0x3f3f3f3f,sum=0;
	for (int d=n;d>=0;d--){
		for (int i=1;i<=m;i++){
			if (a[id[i]].size()<=d) break;
			while (a[id[i]].size()>d){
				int t=*a[id[i]].rbegin();a[id[i]].pop_back();
				tic++;sum+=t;insert(Rot,t,-1);
			}
		}
		if (tic>d) {ans=min(ans,sum);break;} 
		if (d-tic+1<=Rot->s) ans=min(ans,sum+query(Rot,d-tic+1));
	}
	printf("%d",ans);
	return 0;
}
```

