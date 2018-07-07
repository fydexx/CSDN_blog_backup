# 【线段树+Hash】Codeforces 452F Permutation

[题面在这里](http://codeforces.com/problemset/problem/452/F)



按顺序枚举i，则$a_i\pm k$必须在同一侧（即同时出现或同时不出现）

维护权值线段树

其实就是$[a_i-k,a_i]$和$[a_i,a_i+k]$对称

线段树维护正反两个hash值就好了



示例程序：

```C++
#include<cstdio>
#include<algorithm>
#define mp make_pair
#define Fst first
#define Sec second
using namespace std;
typedef unsigned long long ull;
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=(res<<3)+(res<<1)+ch-48,ch=nc();
	return res*f;
}

const int maxn=300005;
int n;
ull p[maxn];
struct node{
	node *l,*r;
	int L,R;ull a,b;
	node () {}
	node (int _L,int _R):L(_L),R(_R),a(0),b(0) {}
	void pushup(){
		if (L==R) return;
		a=l->a*p[r->R - r->L +1]+r->a;
		b=l->b+ r->b*p[l->R - l->L +1];
	}
}base[maxn<<1],nil;
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
void insert(P_node x,int k){
	if (x->L==x->R) {x->a=x->b=1;return;}
	int mid=x->L+x->R>>1;
	if (k<=mid) insert(x->l,k);else insert(x->r,k);
	x->pushup();
}
pair<int,int> A,B;
void queryA(P_node x,int L,int R){
	if (R<x->L||x->R<L) return;
	if (L<=x->L&&x->R<=R) {A.Fst=x->a*p[A.Sec]+A.Fst;A.Sec+=x->R-x->L+1;return;}
	queryA(x->r,L,R);queryA(x->l,L,R);
}
void queryB(P_node x,int L,int R){
	if (R<x->L||x->R<L) return;
	if (L<=x->L&&x->R<=R) {B.Fst=x->b*p[B.Sec]+B.Fst;B.Sec+=x->R-x->L+1;return;}
	queryB(x->l,L,R);queryB(x->r,L,R);
}
int main(){
	n=red();p[0]=1;
	for (int i=1;i<=n;i++) p[i]=p[i-1]*233;
	init();Rot=build(1,n);
	for (int i=1;i<=n;i++){
		int x=red(),k=min(x-1,n-x);
		A=B=mp(0,0);
		queryA(Rot,x-k,x);queryB(Rot,x,x+k);
		if (A.Fst!=B.Fst) return printf("YES"),0;
		insert(Rot,x);
	}
	printf("NO");
	return 0;
}
```

