【前言】
----

Splay Tree，又名伸展树，是OI中应用非常广泛的一种数据结构。相比其他BST，Splay的效率还是不错的。我们一般用Splay维护一个序列。

【操作】
----

Splay有以下几个基本操作：

1.rot旋转
这个不用我多说了，平衡树中很常见的操作

2.splay 伸展
splay(x,k)操作的目的是：从子树（子序列）x中找到第k个，并通过旋转的方式，将其弄到子树的根上。
这里就不得不提一下Splay的独特旋转方式：splay操作中用的是双旋，而非单旋。
所谓双旋，就是一次性考虑两次旋转
情况1：要找的节点正好是x的一个儿子
![这里写图片描述](http://img.blog.csdn.net/20170402131550039?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
这样就只需旋转一次。
情况2：要找的节点与其父节点、当前节点“三点共线”
 ![这里写图片描述](http://img.blog.csdn.net/20170402131559234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
这样可以做两次方向相同的旋转
情况3：三点不共线
 ![这里写图片描述](http://img.blog.csdn.net/20170402131606281?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
这样就要做两次方向相反的旋转
当然，你也可以无视以上规则，直接统统单旋，这样就形成了一个叫Spaly的数据结构，效率非常高（大雾）

3.get_rec获取区间
为了得到区间[L,R]，我们可以先把L-1伸展到根，再把R+1伸展到根的右儿子，这样根的右儿子的左儿子就是我们所需要的子树了。

4.build 构造Splay
这个没什么好讲的，对于区间[L,R]，当前节点为mid，左右子树分别为[L,mid-1]和[mid+1,R]。别忘了maintain。

5.recycle 回收空间
把整棵树遍历一下，每个节点都delete掉即可

【例题】
----

BZOJ 1500
Splay必打模版题，这里有[题解](http://blog.csdn.net/linkfqy/article/details/68951549)

【代码】
----

配上代码：

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