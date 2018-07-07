# 【线段树+Splay】HDU4942 Game on S♂play

[题面在这里](http://acm.hdu.edu.cn/showproblem.php?pid=4942)

其实很简单的一道题……



因为不管怎么旋转，Splay的中序遍历是永远不会变的

所以用一个Splay跟他一起模拟

对于每个节点，维护以下信息：

- 子树区间$[L,R]$
- 子树和$sum$
- 子树大小$size$

然后每次询问就相当于询问一个区间的乘积，旋转就是修改两个点的值

用线段树维护。

复杂度$O(qlogn)$



然而……被卡常了，很不爽……

以下代码正确，但不能过，仅供参考：

```C++
#include<cstdio>
#define LL long long
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
int stk[25];
inline void putint(LL x){
	if (!x) putchar(48);else{
		int top=0;
		while (x) stk[++top]=x%10,x/=10;
		for (int i=top;i;i--) putchar(stk[i]+48);
	}
}

const int maxn=100005,tt=1000000007;
int tst,n,q,times;
int ls[maxn],rs[maxn],w[maxn],sw[maxn],pos[maxn];
namespace S{
	struct node{
		node *l,*r;
		int L,R;LL pro;
		node () {}
		node (int _L,int _R,LL _pro):L(_L),R(_R),pro(_pro) {}
		void pushup() {pro=(l->pro * r->pro)%tt;}
	}nil,base[maxn*2];
	typedef node* P_node;
	P_node null,Rot,len;
	void init(){
		nil=node(0,0,1);null=&nil;
		null->l=null->r=null;len=base;
	}
	P_node newnode(int L,int R,LL pro){
		*len=node(L,R,pro);
		len->l=len->r=null;
		return len++;
	}
	P_node build(int L,int R){
		P_node x=newnode(L,R,1);
		if (L==R) {x->pro=sw[L];return x;}
		int mid=L+R>>1;
		x->l=build(L,mid);x->r=build(mid+1,R);
		x->pushup();return x;
	}
	void insert(P_node &x,int k,LL w){
		if (x->L==x->R) {x->pro=w;return;}
		int mid=x->L + x->R >>1;
		if (k<=mid) insert(x->l,k,w);else insert(x->r,k,w);
		x->pushup();
	}
	LL qry(P_node x,int L,int R){
		if (R<x->L||x->R<L) return 1;
		if (L<=x->L&&x->R<=R) return x->pro;
		return (qry(x->l,L,R)*qry(x->r,L,R))%tt;
	}
}
namespace T{
	struct node{
		node* s[2];
		LL sum,w;int L,R,id,siz;
		node () {}
		node (LL _sum,int _L,int _R,LL _w):sum(_sum),L(_L),R(_R),w(_w) {}
		void maintain(){
			sum=(w + s[0]->sum + s[1]->sum) %tt;
			siz=s[0]->siz + s[1]->siz +1;
		}
	}nil,base[maxn];
	typedef node* P_node;
	P_node null,len,Rot;
	void init(){
		nil=node(0,0,0,0);null=&nil;
		null->s[0]=null->s[1]=null;
		null->siz=0;len=base;
	}
	inline P_node newnode(LL sum,int L,int R,LL w){
		*len=node(sum,L,R,w);len->siz=1;
		len->s[0]=len->s[1]=null;
		return len++;
	}
	void newLR(P_node &x){
		if (x->s[0]==null) x->L=x->id;else x->L=x->s[0]->L;
		if (x->s[1]==null) x->R=x->id;else x->R=x->s[1]->R;
	}
	inline void Rotate(P_node &x,int d){
		P_node k=x->s[d^1];if (k==null) return;
		x->s[d^1]=k->s[d];k->s[d]=x;
		newLR(x);newLR(k);
		x->maintain();k->maintain();
		S::insert(S::Rot,x->id,x->sum); S::insert(S::Rot,k->id,k->sum); x=k;
	}
	void findR(P_node &x,int k,int d){
		if (k<= x->s[0]->siz) findR(x->s[0],k,d);else
		if (k> x->s[0]->siz+1) findR(x->s[1],k- x->s[0]->siz -1,d);else
		Rotate(x,d);
	}
	LL findQ(P_node x,int k){
		if (k<= x->s[0]->siz) return findQ(x->s[0],k);
		if (k> x->s[0]->siz+1) return findQ(x->s[1],k- x->s[0]->siz -1);
		return qry(S::Rot,x->L,x->R);
	}
}
T::P_node dfs(int x){
	using namespace T;
	P_node p=newnode(0,times+1,0,w[x]);
	if (ls[x]) p->s[0]=dfs(ls[x]);
	p->id=++times;int tem=times;
	if (rs[x]) p->s[1]=dfs(rs[x]);
	p->R=times;p->maintain();
	sw[tem]=p->sum;pos[x]=tem;
	return p;
}
int main(){
	tst=red();
	for (int t=1;t<=tst;t++){
		printf("Case #%d:\n",t);
		n=red(),q=red();
		T::init();S::init();
		for (int i=1;i<=n;i++){
			w[i]=red();ls[i]=red();rs[i]=red();
		}
		times=0;T::Rot=dfs(1);
		S::Rot=S::build(1,n);
		while (q--){
			int c=red();
			if (c==0||c==1){
				T::findR(T::Rot,pos[red()],c^1);
			}else{
				putint(T::findQ(T::Rot,pos[red()]));putchar('\n');
			}
		}
	}
	return 0;
}
```



