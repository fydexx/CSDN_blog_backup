【前言】
非旋转Treap，又称FHQ_Treap，函数式Treap。由神犇FHQ首先提出（OrzOrzOrz）。由于使用了函数式编程的思想，非旋转Treap可以实现可持久化。
此外，非旋转Treap的大多数操作都基于分裂与合并，所以代码简短精炼，非常适合OIer食用。
【操作】
就讲讲分裂与合并好了……其他都是可以用脚趾头想出来的。

分裂(split)
对于一棵Treap，我们把它分为key≤k和key>k两个部分。
那么显然地，如果当前key≤k，就往右找下去，并且更新左边的部分。反之亦然。
最后别忘了pushup()

```
void split(P_node x,int k,P_node &l,P_node &r){
	if (x==null) {l=r=null;return;}
	if (x->k <= k) {l=x;split(l->s[1],k,l->s[1],r);}else
	 {r=x;split(r->s[0],k,l,r->s[0]);}
	x->pushup();
}

```


合并(merge)
合并两棵Treap就更简单了，因为A中key均小于B，所以只需判断谁的fix更大就可以确定往左或往右合并。
```
P_node merge(P_node a,P_node b){
	if (a==null) return b;
	if (b==null) return a;
	if (a->f > b->f) {a->s[1]=merge(a->s[1],b); a->pushup(); return a;}
	b->s[0]=merge(a,b->s[0]); b->pushup(); return b;
}

```

【例题】
[BZOJ 3224](http://blog.csdn.net/linkfqy/article/details/70232070)

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