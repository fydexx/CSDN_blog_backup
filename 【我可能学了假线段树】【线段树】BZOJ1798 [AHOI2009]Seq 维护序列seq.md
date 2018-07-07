[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1798)

题解
--

之前写的线段树都是数组版的，代码巨长……
今天试着链式储存线段树，发现极其好用!![这里写图片描述](http://img.blog.csdn.net/20170413212315704?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)![这里写图片描述](http://img.blog.csdn.net/20170413212315704?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)![这里写图片描述](http://img.blog.csdn.net/20170413212315704?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
pushdown,pushup,添加标记等等的操作都可以写成成员函数，代码十分简洁！

而且Insert和Query操作都借鉴了ZH神犇的写法，~~非常省代码~~
所以说，我以前可能学了**假的线段树**

另外，本题有一个注意点，就是如果一个节点同时拥有两种标记，会有优先级的问题
你不知道到底是先加还是先乘
我们可以强行先乘后加，但是如果得到标记的顺序是先加后乘呢？
没有关系，我们可以在进行乘操作时，对加标记也乘一下，就没有问题了

附上代码：

```
#include<cstdio>
#define LL long long
const int maxn=100005,INF=0x3f3f3f3f;
int n,tt,q,a[maxn];
struct node{
	node *l,*r;
	int L,R,s,add,mul;
	node(node *son=NULL,int x=0,int y=0) {l=r=son;L=x;R=y;add=0;mul=1;}
	void pushup() {s=(l->s+r->s)%tt;}
	void tag_add(LL w) {s=((R-L+1)*w%tt+s)%tt; add=(add+w)%tt;}
	void tag_mul(LL w) {s=s*w%tt; add=add*w%tt; mul=mul*w%tt;}
	void pushdown(){
		if (L==R) return;
		if (mul!=1) {l->tag_mul(mul); r->tag_mul(mul); mul=1;}
		if (add!=0) {l->tag_add(add); r->tag_add(add); add=0;}
	}
}nil;
typedef node* P_node;
P_node null,rot;
void Seg_T_init(){
	rot=null=&nil;
	null->l=null->r=null;null->s=0;
}
P_node buildtree(int l,int r){
	P_node x=new node(null,l,r);
	if (l==r) {x->s=a[l]; return x;}
	int mid=l+r>>1;
	x->l=buildtree(l,mid);x->r=buildtree(mid+1,r);
	x->pushup();return x;
}
void ist_add(P_node x,int l,int r,int w){
	if (r<x->L||x->R<l) return;
	x->pushdown();
	if (l<=x->L&&x->R<=r) {x->tag_add(w);return;}
	ist_add(x->l,l,r,w);ist_add(x->r,l,r,w);
	x->pushup();
}
void ist_mul(P_node x,int l,int r,int w){
	if (r<x->L||x->R<l) return;
	x->pushdown();
	if (l<=x->L&&x->R<=r) {x->tag_mul(w);return;}
	ist_mul(x->l,l,r,w);ist_mul(x->r,l,r,w);
	x->pushup();
}
int qry(P_node x,int l,int r){
	if (r<x->L||x->R<l) return 0;
	x->pushdown();
	if (l<=x->L&&x->R<=r) return x->s;
	return (qry(x->l,l,r)+qry(x->r,l,r))%tt;
}
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int main(){
	Seg_T_init();
	n=red(),tt=red();
	for (int i=1;i<=n;i++) a[i]=red();
	rot=buildtree(1,n);q=red();
	while (q--){
		int c=red();
		if (c==1){
			int l=red(),r=red(),w=red();
			ist_mul(rot,l,r,w);
		}else
		if (c==2){
			int l=red(),r=red(),w=red();
			ist_add(rot,l,r,w);
		}else{
			int l=red(),r=red();
			printf("%d\n",qry(rot,l,r));
		}
	}
	return 0;
}
```