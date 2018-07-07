# 【主席树】BZOJ3524 [Poi2014]Couriers

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3524)



主席树裸题……

每次找到最多的那个值就好了

如果最多的都没有超过$r-l+1 \over 2$，就是0了



示例程序：

```C++
#include<cstdio>
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

const int maxn=500005,maxs=11000000;
int n,q;
struct node{
	node *l,*r;
	int L,R,s;
	node () {}
	node (int _L,int _R):L(_L),R(_R),s(0) {}
	void pushup() {s=l->s+r->s;}
}nil,base[maxs];
typedef node* P_node;
P_node null,len,Rot[maxn];
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
P_node insert(P_node fa,int k){
	P_node x=newnode(fa->L,fa->R);x->s=fa->s;
	if (x->L==x->R) {x->s++;return x;}
	x->l=fa->l; x->r=fa->r;
	int mid=x->L+x->R>>1;
	if (k<=mid) x->l=insert(fa->l,k);else x->r=insert(fa->r,k);
	x->pushup(); return x;
}
pair<int,int> query(P_node l,P_node r){
	if (l->L==l->R) return make_pair(r->s - l->s,l->L);
	int lsum=r->l->s - l->l->s,rsum=r->r->s - l->r->s;
	if (lsum>rsum) return query(l->l,r->l);else return query(l->r,r->r);
}
int main(){
	n=red(),q=red();
	init();Rot[0]=build(1,n);
	for (int i=1;i<=n;i++) Rot[i]=insert(Rot[i-1],red());
	while (q--){
		int l=red(),r=red();
		pair<int,int> x=query(Rot[l-1],Rot[r]);
		if (x.first*2>r-l+1) printf("%d\n",x.second);else printf("0\n");
	}
	return 0;
}          
```

