# 【线段树，Treap】BZOJ2770 YY的Treap

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2770)



没什么好说的，和[这道题](http://blog.csdn.net/linkfqy/article/details/77511662)一样的



就是要注意：虽然不会同时出现key与priority

但还是有可能有相同的值不同时出现

所以不能偷懒用map



示例程序：

```C++
#include<cstdio>
#include<map>
#include<algorithm>
#define LL long long
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

const int maxn=400005;
const LL INF=(LL)1<<60;
int n,q;
map<int,int> id;
struct qry{
	char c;int a,b;
}Q[maxn];
struct data{
	int k,w;
	data () {}
	data (int _k,int _w):k(_k),w(_w) {}
	bool operator<(const data&b)const{
		return k<b.k;
	}
	bool operator==(const data&b)const{
		return k==b.k;
	}
}a[maxn];
struct node{
	node *l,*r;
	int L,R;LL mn;
	node () {}
	node (int _L,int _R,LL _mn):L(_L),R(_R),mn(_mn) {}
	void maintain() {mn=min(l->mn,r->mn);}
}base[maxn*2],nil;
typedef node* P_node;
P_node null,Rot,len;
void init(){
	nil=node(0,0,INF);Rot=null=&nil;
	null->l=null->r=null;len=base;
}
P_node newnode(int L,int R,LL mn){
	*len=node(L,R,mn);
	len->l=len->r=null;
	return len++;
}
P_node build(int L,int R){
	P_node x=newnode(L,R,INF);
	if (L==R) return x;
	int mid=L+R>>1;
	x->l=build(L,mid);x->r=build(mid+1,R);
	x->maintain();return x;
}
void insert(P_node &x,int k,LL w){
	if (x->L==x->R) {x->mn=w;return;}
	int mid=x->L+x->R>>1;
	if (k<=mid) insert(x->l,k,w);else insert(x->r,k,w);
	x->maintain();
	return;
}
LL query(P_node x,int L,int R){
	if (R< x->L || x->R <L) return INF;
	if (L<= x->L && x->R <=R) return x->mn;
	return min(query(x->l,L,R),query(x->r,L,R));
}
int Bin(int x){
	return lower_bound(a+1,a+1+n,data(x,0))-a;
}
int main(){
	n=red(),q=red();int nn=n;
	for (int i=1;i<=n;i++) a[i].k=red();
	for (int i=1;i<=n;i++) a[i].w=red();
	for (int i=1;i<=nn;i++) Q[i].c='I',Q[i].a=a[i].k,Q[i].b=a[i].w;
	q+=nn;
	for (int i=nn+1;i<=q;i++){
		Q[i].c=fstchar();
		if (Q[i].c=='I'){
			a[++n].k=Q[i].a=red(),a[n].w=Q[i].b=red();
		}else
		if (Q[i].c=='D') Q[i].a=red();else
		 Q[i].a=red(),Q[i].b=red();
	}
	
	sort(a+1,a+1+n);
	n=unique(a+1,a+1+n)-a-1;
	init();Rot=build(1,n);
	
	for (int i=1;i<=q;i++){
		if (Q[i].c=='I'){
			int k=Bin(Q[i].a);id[Q[i].b]=Q[i].a;
			insert(Rot,k,Q[i].b);
		}else
		if (Q[i].c=='D'){
			int k=Bin(Q[i].a);
			insert(Rot,k,INF);
		}else{
			int x=Bin(Q[i].a),y=Bin(Q[i].b);
			if (x>y) swap(x,y);
			printf("%d\n",id[(int)query(Rot,x,y)]);
		}
	}
	return 0;
}
```

