[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1012)

~~又在A水题……~~

这个不用我多说了吧，随便套个什么数据结构就行了

示例程序

```
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn=400005;
int q,tt,n,lst;
struct node{
	node *l,*r;
	int L,R,mx;
	node() {}
	node(int x,int y,int w):L(x),R(y),mx(w) {}
	void maintain() {mx=max(l->mx,r->mx);}
}base[maxn],nil;
typedef node* P_node;
P_node null,len,Rot;
void init(){
	nil=node(0,0,0);null=&nil;
	null->l=null->r=null;len=base;
}
P_node newnode(int x,int y,int w){
	*len=node(x,y,w);len->l=len->r=null;
	return len++;
}
P_node build(int L,int R){
	P_node x=newnode(L,R,0);
	if (L==R) return x;
	int mid=L+R>>1;
	x->l=build(L,mid);x->r=build(mid+1,R);
	return x;
}
void ist(P_node &x,int k,int w){
	if (x->L==x->R){x->mx=w;return;}
	int mid=x->L+x->R>>1;
	if (k<=mid) ist(x->l,k,w);else ist(x->r,k,w);
	x->maintain();
}
int qry(P_node x,int L,int R){
	if (x->R<L||R<x->L) return -1;
	if (L<=x->L&&x->R<=R) return x->mx;
	return max(qry(x->l,L,R),qry(x->r,L,R));
}
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
main(){
	q=red(),tt=red(),n=lst=0;
	init();Rot=build(1,q);
	while (q--)
	 if (fstchar()=='Q'){
	 	int l=red();
	 	printf("%d\n",lst=qry(Rot,n-l+1,n));
	 }else{
	 	int x=red();
	 	ist(Rot,++n,(x+lst)%tt);
	 }
	return 0;
}
```