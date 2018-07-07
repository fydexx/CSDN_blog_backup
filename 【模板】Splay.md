Splay适用于维护序列
用起来很方便，而且很好打

参考博客：[Splay-总结](http://blog.csdn.net/linkfqy/article/details/68951736)

```
#include<cstdio>
#include<algorithm>
#define Rec rt->s[1]->s[0]
using namespace std;
const int maxn=500005,INF=0x3f3f3f3f;
int n,q,a[maxn];
char str[15];
struct node{
	node* s[2];
	int x,size,sum,same,lm,rm,ma;
	bool flip;
	node(int h=0,node *son=NULL){s[0]=s[1]=son;x=sum=lm=rm=ma=h;size=1;same=INF;flip=0;}
	int cmp(int &k){
		if (k<=s[0]->size) return 0;
		if (k>s[0]->size+1) {k-=s[0]->size+1;return 1;}
		return -1;
	}
	void maintain(){
		size=s[0]->size+s[1]->size+1;
		sum=s[0]->sum+s[1]->sum+x;
		lm=max(s[0]->lm,max(s[0]->sum+x,s[0]->sum+x+s[1]->lm));
		rm=max(s[1]->rm,max(s[1]->sum+x,s[1]->sum+x+s[0]->rm));
		ma=max(max(max(s[0]->ma,s[1]->ma),max(s[0]->rm+x,s[1]->lm+x)),max(x,s[0]->rm+x+s[1]->lm));
	}
	void add_same(int k) {x=same=k;sum=k*size;lm=rm=ma=(k>0?sum:k);}
	void add_flip() {swap(s[0],s[1]);swap(lm,rm);flip^=1;}
	void pushdown(){
		if (same!=INF) s[0]->add_same(same),s[1]->add_same(same),same=INF;
		if (flip) s[0]->add_flip(),s[1]->add_flip(),flip=0;
	}
}nil;
typedef node* P_node;
P_node null,rt;
void splay_init(){
	rt=null=&nil;
	null->s[0]=null->s[1]=null;
	null->size=null->x=null->sum=0;
	null->lm=null->rm=null->ma=-INF;
	null->same=INF;null->flip=0;
}
void recycle(P_node &x){
	if (x==null) return;
	recycle(x->s[0]);recycle(x->s[1]);
	delete x;
}
void rot(P_node &x,int d){
	P_node k=x->s[d^1];x->s[d^1]=k->s[d];k->s[d]=x;
	x->maintain();k->maintain();x=k;
}
void splay(P_node &x,int k){
	x->pushdown();
	int d1=x->cmp(k);
	if (d1!=-1){
		x->s[d1]->pushdown();
		int d2=x->s[d1]->cmp(k);
		if (d2!=-1){
			splay(x->s[d1]->s[d2],k);
			if (d1==d2) rot(x,d1^1),rot(x,d1^1);else
			 rot(x->s[d1],d2^1),rot(x,d1^1);
		}else rot(x,d1^1);
	}
}
P_node build(int l,int r){
	if (l>r) return null;
	int mid=l+r>>1;P_node p=new node(a[mid],null);
	p->s[0]=build(l,mid-1);p->s[1]=build(mid+1,r);
	p->maintain();
	return p;
}
void get_rec(P_node &x,int l,int r) {splay(x,l-1);splay(x->s[1],r-l+2);}
void print(P_node x){
	if (x==null) return;x->pushdown();x->maintain();
	print(x->s[0]);
	printf("%d ",x->x);
	print(x->s[1]);
}
int main(){
	splay_init();
	//do something...
	return 0;
}
```