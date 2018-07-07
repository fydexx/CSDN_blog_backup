[题面在这里](http://poj.org/problem?id=2104)

一道典型的主席树模板题。

关于主席树[看这里](http://blog.csdn.net/linkfqy/article/details/70183258)

首先考虑线段树表示的是什么
我们定义线段[L,R]表示所有满足L<=a[i]<=R的个数（注意不要和上面的区间[L,R]搞混）。 
那么思考如下过程：从1至n，一个一个对线段树插入a[i]。 
这样就可以构造出主席树。
其中第i棵线段树（第i个历史版本）rot[i]的节点[L,R]的权值表示[1,i]这个子区间的所有满足L<=a[i]<=R的个数。 
询问与BST的Rank操作类似，考虑在rot[i]中找第K大的数
如果左子树的权值大于等于K，说明第k大的在左边，否则在右边，并修改K的值。
这样询问rot[R]和rot[L-1]两次即可得到答案。（实际运用中我把两次询问做到了一起）

```
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn=100005,maxs=2000005;
int n,m,q,a[maxn],b[maxn];
struct node{
	node *l,*r;
	int s,L,R;
	node(int x=0,int y=0) {L=x;R=y;s=0;}
	void pushup() {s=l->s+r->s;}
}base[maxs],nil;
typedef node* P_node;
P_node null=&nil,len=base,rot[maxn];
void Seg_T_init(){
	null->l=null->r=null;
}
P_node newnode(int l,int r){
	*len=node(l,r);
	return len++;
}
P_node build(int l,int r){
	P_node x=newnode(l,r);
	if (l==r) return x;
	int mid=l+r>>1;
	x->l=build(l,mid);x->r=build(mid+1,r);
	x->pushup();
	return x;
}
P_node get_link(P_node lst,int k){
	P_node x=newnode(lst->L,lst->R); x->s=lst->s;
	x->l=lst->l; x->r=lst->r;
	if (x->L==x->R) {x->s++;return x;}
	int mid=x->L+x->R>>1;
	if (k<=mid) x->l=get_link(lst->l,k);else
	 x->r=get_link(lst->r,k);
	x->pushup();
	return x;
}
int qry(P_node l,P_node r,int k){
	if (l->L==l->R) return b[l->L];
	int tem=r->l->s-l->l->s;
	if (k<=tem) return qry(l->l,r->l,k);else return qry(l->r,r->r,k-tem);
}
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int main(){
	Seg_T_init();
	n=red(),q=red();
	for (int i=1;i<=n;i++) a[i]=b[i]=red();
	sort(b+1,b+1+n);
	m=unique(b+1,b+1+n)-b-1;
	rot[0]=build(1,m);
	for (int i=1;i<=n;i++)
	 rot[i]=get_link(rot[i-1],lower_bound(b+1,b+1+m,a[i])-b);
	while (q--){
		int l=red(),r=red(),k=red();
		printf("%d\n",qry(rot[l-1],rot[r],k));
	}
	return 0;
}
```