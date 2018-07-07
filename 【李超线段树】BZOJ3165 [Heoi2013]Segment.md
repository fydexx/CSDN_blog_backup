#【李超线段树】BZOJ3165 [Heoi2013]Segment

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3165)



李超线段树的裸题，不解释



示例程序：

```C++
#include<cstdio>
#include<cmath>
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

const int maxn=40005,xp=39989,yp=1e9;
const double eps=1e-8;
int q,lastans=0,tot=0;
struct line{
	double k,b;int id;
	line () {id=0;}
	line (double _k,double _b,int _id):k(_k),b(_b),id(_id) {}
};
inline line add(int a,int b,int c,int d){
	if (a==c) return line(0,max(b,d),++tot);
	double k=((double)d-b)/(c-a);
	return line(k,b-a*k,++tot);
}
inline int cmp(double x){
	if (fabs(x)<=eps) return 0;
	return x>0?1:-1;
}
#define Y(l,x) ((l).k*(x)+(l).b)
inline bool higher(line a,line b,int k){
	if (cmp(Y(a,k)-Y(b,k))==0) return a.id<b.id;
	return cmp(Y(a,k)-Y(b,k))>0;
}

struct node{
	node *l,*r;
	int L,R;line ll;
	node () {}
	node (int _L,int _R):L(_L),R(_R) {}
}nil,base[maxn*4];
typedef node* P_node;
P_node null,Rot,len;
void init(){
	nil=node(0,0);null=&nil;
	null->l=null->r=null;len=base;
}
P_node newnode(int L,int R){
	*len=node(L,R); len->l=len->r=null;
	return len++;
}
P_node build(int L,int R){
	P_node x=newnode(L,R);
	if (L==R) return x;
	int mid=L+R>>1;
	x->l=build(L,mid); x->r=build(mid+1,R);
	return x;
}
void pushdown(P_node x,line l){
	if (x->ll.id==0) {x->ll=l;return;}
	if (higher(l,x->ll,x->L)) swap(l,x->ll);
	if (x->L==x->R||cmp(x->ll.k - l.k)==0) return;
	double pos=(x->ll.b - l.b)/(l.k - x->ll.k);
	if (pos<x->L||x->R<pos) return;
	int mid=x->L+x->R>>1;
	if (pos<=mid) pushdown(x->l,x->ll),x->ll=l;else pushdown(x->r,l);
}
void insert(P_node x,int L,int R,line l){
	if (R<x->L||x->R<L) return;
	if (L<=x->L&&x->R<=R) {pushdown(x,l);return;}
	insert(x->l,L,R,l); insert(x->r,L,R,l);
}
line query(P_node x,int pos){
	if (x->L==x->R) return x->ll;
	int mid=x->L+x->R>>1;line ans;
	if (pos<=mid) ans=query(x->l,pos);else ans=query(x->r,pos);
	if (higher(x->ll,ans,pos)) ans=x->ll;
	return ans;
}
int main(){
	q=red();
	init();Rot=build(1,xp+10);
	while (q--)
	 if (red()){
	 	int a=(red()+lastans-1)%xp+1,b=(red()+lastans-1)%yp+1,c=(red()+lastans-1)%xp+1,d=(red()+lastans-1)%yp+1;
	 	if (a>c) swap(a,c),swap(b,d);
	 	insert(Rot,a,c,add(a,b,c,d));
	 }else{
	 	int p=(red()+lastans-1)%xp+1;
	 	printf("%d\n",lastans=query(Rot,p).id);
	 }
	return 0;
}
```

