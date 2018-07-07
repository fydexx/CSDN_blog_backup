[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3224)

平衡树裸题……
写了Treap/非旋转Treap两个版本。

【普通Treap】

```
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn=100005,INF=0x3f3f3f3f;
int q;
struct node{
	node* s[2];
	int k,f,size,cnt;
	void maintain() {size=s[0]->size+s[1]->size+cnt;}
};
typedef node* P_node;
struct treap{
	node tree[maxn],nil;
	P_node null,len,root;
	treap(){
		root=null=&nil;
		null->s[0]=null->s[1]=null;
		null->size=null->cnt=0;
		len=tree;
	}
	P_node newnode(int key){
		len->k=key;len->f=rand();
		len->s[0]=len->s[1]=null;
		len->size=len->cnt=1;
		return len++;
	}
	void rot(P_node &x,int d){
		P_node k=x->s[d^1];x->s[d^1]=k->s[d];k->s[d]=x;
		x->maintain();k->maintain();x=k;
	}
	void insert(P_node &x,int key){
		if (x==null) x=newnode(key);else
		if (x->k==key) x->cnt++;else{
			int d=key>x->k;
			insert(x->s[d],key);if (x->s[d]->f > x->f) rot(x,d^1);
		}
		x->maintain();
	}
	void erase(P_node &x,int key){
		if (x->k==key){
			if (x->cnt>1) x->cnt--;else
			 if (x->s[0]==null) x=x->s[1];else
			 if (x->s[1]==null) x=x->s[0];else{
			 	int d=x->s[0]->f < x->s[1]->f;
			 	rot(x,d^1);erase(x->s[d^1],key);
			 }
		}else erase(x->s[key>x->k],key);
		x->maintain();
	}
	int kth(P_node &x,int k){
		if (x==null||k<1||k>x->size) return 0;
		if (k<=x->s[0]->size) return kth(x->s[0],k);else
		if (k>x->s[0]->size+x->cnt) return kth(x->s[1],k-x->s[0]->size-x->cnt);
		return x->k;
	}
	int rank(P_node &x,int key){
		if (x->k==key) return x->s[0]->size+1;
		if (key<x->k) return rank(x->s[0],key);
		return rank(x->s[1],key)+x->s[0]->size+x->cnt;
	}
	int pred(P_node &x,int key){
		if (x==null) return -INF;
		if (key<=x->k) return pred(x->s[0],key);
		return max(x->k,pred(x->s[1],key));
	}
	int succ(P_node &x,int key){
		if (x==null) return INF;
		if (key>=x->k) return succ(x->s[1],key);
		return min(x->k,succ(x->s[0],key));
	}
	void print(P_node &x){
		if (x==null) return;
		print(x->s[0]);
		for (int i=1;i<=x->cnt;i++) printf("%d ",x->k);
		print(x->s[1]);
	}
	void Insert(int x) {insert(root,x);}
	void Erase(int x) {erase(root,x);}
	int Rank(int x) {return rank(root,x);}
	int Kth(int x) {return kth(root,x);}
	int Pred(int x) {return pred(root,x);}
	int Succ(int x) {return succ(root,x);}
	void Print() {print(root);printf("**\n");}
}T;
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int main(){
	q=red();
	while (q--){
		int c=red(),x=red();
		if (c==1) T.Insert(x);else
		if (c==2) T.Erase(x);else
		if (c==3) printf("%d\n",T.Rank(x));else
		if (c==4) printf("%d\n",T.Kth(x));else
		if (c==5) printf("%d\n",T.Pred(x));else
		if (c==6) printf("%d\n",T.Succ(x));
	}
	return 0;
}
```

【非旋转Treap】

```
#include<cstdio>
#include<cstdlib>
const int maxn=100005;
struct node{
	node *s[2];
	int k,f,siz;
	node () {}
	node (int w,node *son) {s[0]=s[1]=son;k=w;f=rand();siz=1;}
	void pushup() {siz=s[0]->siz+s[1]->siz+1;}
}nil,base[maxn];
typedef node* P_node;
P_node null,len,Root;
void treap_init(){
	nil=node(0,NULL);null=&nil;
	null->s[0]=null->s[1]=null;
	null->siz=0; len=base; Root=null;
}
P_node newnode(int x){
	*len=node(x,null);
	return len++;
}
P_node merge(P_node a,P_node b){
	if (a==null) return b;
	if (b==null) return a;
	if (a->f > b->f) {a->s[1]=merge(a->s[1],b); a->pushup(); return a;}
	b->s[0]=merge(a,b->s[0]); b->pushup(); return b;
}
void split(P_node x,int k,P_node &l,P_node &r){
	if (x==null) {l=r=null;return;}
	if (x->k <= k) {l=x;split(l->s[1],k,l->s[1],r);}else
	 {r=x;split(r->s[0],k,l,r->s[0]);}
	x->pushup();
}
void Insert(int k){
	P_node l,r;
	split(Root,k,l,r);
	Root=merge(merge(l,newnode(k)),r);
}
void Erase(int k){
	P_node l,mid,r;
	split(Root,k-1,l,r);split(r,k,mid,r);
	Root=merge(l,merge(merge(mid->s[0],mid->s[1]),r));
}
int Kth(P_node x,int k){
	if (k <= x->s[0]->siz) return Kth(x->s[0],k);else
	if (k > x->s[0]->siz+1) return Kth(x->s[1],k - x->s[0]->siz -1);
	return x->k;
}
int get_edge(P_node x,int d){
	while (x->s[d]!=null) x=x->s[d];
	return x->k;
}
int q;
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int main(){
	treap_init();
	q=red();
	while (q--){
		int c=red();
		if (c==1) Insert(red());else
		if (c==2) Erase(red());else
		if (c==3){
			int k=red();P_node l,r;
			split(Root,k-1,l,r); printf("%d\n",l->siz+1); Root=merge(l,r);
		}else
		if (c==4) printf("%d\n",Kth(Root,red()));else
		if (c==5){
			int k=red();P_node l,r;
			split(Root,k-1,l,r); printf("%d\n",get_edge(l,1)); Root=merge(l,r);
		}else{
			int k=red();P_node l,r;
			split(Root,k,l,r); printf("%d\n",get_edge(r,0)); Root=merge(l,r);
		}
	}
	return 0;
}
```