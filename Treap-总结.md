【前(fei)言(hua)】
--------------

对于BST，相信大家都很熟悉了。BST系列的数据结构功能都比较强大，但是朴素BST的效率实在不敢恭维，非常容易被卡。今天介绍的Treap是BST的加强版，可以有效防止被卡。

【Treap的性质】
----------

BST为什么这么容易卡呢？究其原因，其实是BST不太平衡导致的。所谓平衡，就是要让BST的左右两棵子树深度尽量相同，这样在操作时就不会往下查找过多的次数。
于是，我们可以在Tree中加入Heap，就形成了Treap。Treap在满足BST性质的同时，另外添加了一个优先级fix，fix满足堆性质（即任意节点的两个儿子fix都小于/大于自己）本文以大根堆为例。
那么很显然，如果fix的取值比较均匀，为了满足堆性质，Treap作为一个BST就会比较平衡，一般使用随机数给fix赋值。

【Treap的储存方法】
------------

我们定义如下结构体作为Treap的节点：

```
struct node{
	node* s[2];
	int k,f,size,cnt;
	void maintain() {size=s[0]->size+s[1]->size+cnt;}
};
```

typedef node* P_node;
其中k表示Treap作为BST的键值key，f表示Treap作为Heap的优先级fix，size和cnt分别表示子树的大小、相同key的个数。maintain()成员函数用于更新size的值。
一般使用node型指针来表示一棵子树，这样比较方便。对x进行修改时，不用考虑指向x的变量。
另外，Treap结构体也值得注意：

```
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
	……
};
```

构造函数主要是定义了null这个空指针，以及根节点的初值。
函数newnode(int)返回一个全新的节点，其键值为给定参数。

【Treap的操作】
----------

Treap支持以下几种操作：

 - Rotate 旋转给定子树
 - Insert 插入给定键值
 - Erase 删除给定键值
 - Kth 查找第k小的键值
 - Rank 查找给定键值的排名
 - Pred 查找给定键值的前驱
 - Succ 查找给定键值 的后继

先来讲讲旋转操作：
旋转，是在不破坏BST性质的前提下，改变节点间的父子关系，用于维护堆性质。有左旋和右旋两种。
![这里写图片描述](http://img.blog.csdn.net/20170305154345497?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
规定左旋为0，右旋为1，可以得到如下代码：

```
void rot(P_node &x,int d){
	P_node k=x->s[d^1];x->s[d^1]=k->s[d];k->s[d]=x;
	x->maintain();k->maintain();x=k;
}
```

注意旋转完以后，原来以x为根的子树就变成以k为根了，所以要记得x=k。

再来看插入操作：
和普通BST一样，判断应该插入左子树还是右子树，递归处理。最后的结果一定是新的节点是叶节点。这样就已经满足了BST性质，但是不一定满足堆性质，于是就不断旋转，直至满足堆性质。

```
void insert(P_node &x,int key){
	if (x==null) x=newnode(key);else
	if (x->k==key) x->cnt++;else{
		int d=key>x->k;
		insert(x->s[d],key);if (x->s[d]->f > x->f) rot(x,d^1);
	}
	x->maintain();
}
```

别忘了更新size。

删除操作与插入相反，找到要删除的节点后，将其一路旋转，最终搞到叶子节点直接删除。

```
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
```

别忘了更新size。

我们来考虑这样一个问题：节点x的键值在所有键值中的排名属于什么范围？显然，因为x的左子树都比x小，而x->k有cnt个，那么范围就是[x->s[0]->size+1,x->s[0]->size+x->cnt]，于是可以得到如下代码：

```
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
```

最后是前驱、后继操作。没什么好讲的，只要记住：前驱是比key小的最大的键值，后继是比key大的最小的键值。

```
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
```

完整的模板如下：

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

【Treap的效率】
----------

    Treap作为一颗平衡的BST，可以证明，每次操作的时间复杂度的期望值为O(logn)，而且Treap的编程复杂度比较低，是竞赛常用的数据结构。


