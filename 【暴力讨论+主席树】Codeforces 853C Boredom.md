# 【暴力讨论+主席树】Codeforces 853C Boredom

[题面在这里](http://codeforces.com/problemset/problem/853/C)



按照每次询问的子矩阵，把整个矩阵分为9块：

```
1 2 3
4 5 6
7 8 9
```

（块5是询问的子矩阵）

那么块1与块5,6,8,9中的点有贡献，累计$sum(1)*sum(5+6+8+9)$（sum表示这个块里点的个数）

……

以此类推，暴力讨论每个块的贡献就好了

怎么求sum？

由于每一列有且只有1个点

其实就是求一个序列的指定区间中值属于某个范围的个数

用主席树维护就好了



示例程序：

```C++
#include<cstdio>
#define sum(a,b,c,d) query(Rot[a-1],Rot[b],c,d)
typedef long long LL;
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

const int maxn=200005,maxs=8000005;
int n,q;
struct node{
	node *l,*r;
	int L,R,s;
	node () {}
	node (int _L,int _R,int _s):L(_L),R(_R),s(_s) {}
	void pushup() {s=l->s+r->s;}
}base[maxs],nil;
typedef node* P_node;
P_node null,len,Rot[maxn];
void init(){
	nil=node(0,0,0);null=&nil;
	null->l=null->r=null;len=base;
}
P_node newnode(int L,int R){
	*len=node(L,R,0);
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
	P_node x=newnode(fa->L,fa->R);
	if (fa->L==fa->R) {x->s=fa->s+1;return x;}
	x->l=fa->l; x->r=fa->r;
	int mid=x->L+x->R>>1;
	if (k<=mid) x->l=insert(fa->l,k);
	else x->r=insert(fa->r,k);
	x->pushup(); return x;
}
int query(P_node l,P_node r,int L,int R){
	if (R<l->L||l->R<L) return 0;
	if (L<=l->L&&l->R<=R) return r->s - l->s;
	return query(l->l,r->l,L,R)+query(l->r,r->r,L,R);
}
int main(){
	n=red(),q=red();
	init();Rot[0]=build(1,n);
	for (int i=1;i<=n;i++)
	 Rot[i]=insert(Rot[i-1],red());
	while (q--){
		int l=red(),d=red(),r=red(),u=red();
		LL x=sum(l,r,d,u),res=(x-1)*x+(n-x)*x;
		x=sum(1,l-1,u+1,n);res+=x*sum(l,n,1,u);
		x=sum(l,r,u+1,n);res+=x*sum(1,n,1,u);
		x=sum(r+1,n,u+1,n);res+=x*sum(1,r,1,u);
		x=sum(1,l-1,d,u);res+=x*sum(l,n,1,n);
		x=sum(r+1,n,d,u);res+=x*sum(1,r,1,n);
		x=sum(1,l-1,1,d-1);res+=x*sum(l,n,d,n);
		x=sum(l,r,1,d-1);res+=x*sum(1,n,d,n);
		x=sum(r+1,n,1,d-1);res+=x*sum(1,r,d,n);
		printf("%lld\n",res/2);
	}
	return 0;
}
```

