# 【线段树，Treap】CodeChef COT5 Count on a Treap

[题面在这里](https://www.codechef.com/problems/COT5)

这道题很考验对Treap的理解

考虑问题时，先把Treap转换成LNR序列，那么就只用考虑节点的权值了
首先发现，Treap中一个节点的father必定是：
向两边找第一个权值比自己大的点
那么显然一个节点的深度可以由 向两边扩展的最长上升序列的长度 得到
我们用线段树维护这个值
具体做法是离线得到所有键值，维护某区间内的最长上升序列的长度
区间询问一发就可以得到一个点的深度
两个点的LCA其实就是对应区间的最大权值
然后就很好回答两点间距离了

现在讨论如何维护最长上升序列的长度
其实只要会update就可以了
定义$find(x,a)$表示在x节点对应的序列前面加一个a的 最长上升序列的长度

  1. 如果a大于x->lson的最大值，则`find(x,a)=find(x->rson,a)`
  2. 否则`find(x,a)=find(x->lson,a) + x->t - x->l->t`

然后考虑update：
`x->t = x->l->t + find(x->r,x->l->max)`
没什么好说的吧……

示例程序：

```
#include<cstdio>
#include<algorithm>
#include<map>
#define uint unsigned long
using namespace std;
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline uint red(){
	uint res=0;char ch=nc();
	while (ch<'0'||'9'<ch) ch=nc();
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res;
}

const int maxn=200000;
map<uint,int> id;
int n,q;
struct data{
	uint k,w;
	bool operator<(const data&b)const{
		return k<b.k;
	}
}a[maxn];
struct qry{
	int d;uint a,b;
}Q[maxn];
struct M_Seg{
	struct node{
		node *l,*r;
		uint mx;int L,R;
		node () {}
		node (uint _m,int _L,int _R):mx(_m),L(_L),R(_R) {}
		void maintain() {mx=max(l->mx,r->mx);}
	}base[maxn*2],nil;
	typedef node* P_node;
	P_node null,len,Rot;
	void init(){
		nil=node(0,0,0);null=&nil;
		null->l=null->r=null;Rot=null;len=base;
	}
	P_node newnode(uint mx,int L,int R){
		*len=node(mx,L,R);
		len->l=len->r=null;
		return len++;
	}
	P_node build(int L,int R){
		P_node x=newnode(0,L,R);
		if (L==R) return x;
		int mid=L+R>>1;
		x->l=build(L,mid);x->r=build(mid+1,R);
		x->maintain(); return x;
	}
	void insert(P_node &x,int k,uint w){
		if (x->L==x->R){x->mx=w;return;}
		int mid=x->L+x->R>>1;
		if (k<=mid) insert(x->l,k,w);else insert(x->r,k,w);
		x->maintain();
	}
	P_node query(P_node x,int L,int R){
		if (x->R<L||R<x->L) return null;
		if (L<=x->L&&x->R<=R) return x;
		P_node lx=query(x->l,L,R),rx=query(x->r,L,R);
		if (lx->mx > rx->mx) return lx;else return rx;
	}
	int Query(int L,int R){
		return query(Rot,L,R)->L;
	}
}Max;
struct Seg{
	struct node{
		node *l,*r;
		uint mx,t;int L,R;
		node () {}
		node (uint _t,uint _m,int _L,int _R):t(_t),mx(_m),L(_L),R(_R) {}
	}base[maxn*2],nil;
	typedef node* P_node;
	P_node null,len,Rot;
	int find(P_node x,int a){
		if (x->L==x->R) return a<x->mx;
		if (a > x->l->mx) return find(x->r,a);
		return find(x->l,a)+ x->t - x->l->t;
	}
	void update(P_node &x){
		x->mx=max(x->l->mx,x->r->mx);
		x->t=x->l->t + find(x->r,x->l->mx);
	}
	void init(){
		nil=node(0,0,0,0);null=&nil;
		null->l=null->r=null;Rot=null;len=base;
	}
	P_node newnode(uint t,uint mx,int L,int R){
		*len=node(t,mx,L,R);
		len->l=len->r=null;
		return len++;
	}
	P_node build(int L,int R){
		P_node x=newnode(1,0,L,R);
		if (L==R) return x;
		int mid=L+R>>1;
		x->l=build(L,mid);x->r=build(mid+1,R);
		update(x); return x;
	}
	void insert(P_node &x,int k,uint w){
		if (x->L==x->R) {x->mx=w;x->t=1;return;}
		int mid=x->L+x->R>>1;
		if (k<=mid) insert(x->l,k,w);else insert(x->r,k,w);
		update(x);
	}
	int res;uint lst;
	void query(P_node x,int L,int R){
		if (R<x->L||x->R<L) return;
		if (L<=x->L&&x->R<=R){
			res+=find(x,lst);
			lst=max(lst,x->mx);
			return;
		}
		query(x->l,L,R);query(x->r,L,R);
	}
	int Query(int k,uint w){
		if (k==n) return 1;
		res=1;lst=w;
		query(Rot,k+1,n);
		return res;
	}
}L,R;
int Dist(int k){
	return L.Query(k,a[k].w)+R.Query(n-k+1,a[k].w)-2;
}
int main(){
	q=red();n=0;
	for (int i=1;i<=q;i++){
		Q[i].d=red();
		if (Q[i].d==0)
		 a[++n].k=Q[i].a=red(),a[n].w=Q[i].b=red();
		else
		if (Q[i].d==1)
		 Q[i].a=red();
		else Q[i].a=red(),Q[i].b=red();
	}
	sort(a+1,a+1+n);
	for (int i=1;i<=n;i++) id[a[i].k]=i;
	Max.init();L.init();R.init();
	Max.Rot=Max.build(1,n);
	L.Rot=L.build(1,n);
	R.Rot=R.build(1,n);
	for (int i=1;i<=q;i++)
	 if (Q[i].d==0){
	 	int k=id[Q[i].a];
	 	Max.insert(Max.Rot,k,Q[i].b);
	 	L.insert(L.Rot,k,Q[i].b);
	 	R.insert(R.Rot,n-k+1,Q[i].b);
	 }else
	 if (Q[i].d==1){
	 	int k=id[Q[i].a];
	 	Max.insert(Max.Rot,k,0);
	 	L.insert(L.Rot,k,0);
	 	R.insert(R.Rot,n-k+1,0);
	 }else{
	 	int u=id[Q[i].a],v=id[Q[i].b];
	 	if (u>v) swap(u,v);
	 	int lca=Max.Query(u,v);
	 	printf("%d\n",Dist(u)+Dist(v)-Dist(lca)*2);
	 }
	return 0;
}
```