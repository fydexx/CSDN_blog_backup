[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1878)

这道题有三种解法，以下分别介绍（等我A掉会补全）

【主席树】
-----
关于主席树[戳这里](http://blog.csdn.net/linkfqy/article/details/70183258)

造一个lst[i]表示位置i的这个数上一次出现的位置（如果没有就是0）
那么对于每次询问L~R范围里的数字种数
其实就是询问对于$L≤i≤R$，满足$lst[i]<L$的个数
那么就很好搞了，对lst造N棵值域线段树
询问就直接找到L-1这个位置看它前面有几个
复杂度$O(Qlogn)$

附上代码：

```
#include<cstdio>
const int maxn=50005,maxs=1000005;
int n,q,a[maxn],lst[maxs];
struct node{
	node *l,*r;
	int L,R,s;
	node () {}
	node (int x,int y):L(x),R(y),s(0) {}
	void pushup() {s=l->s + r->s;}
}nil,base[maxs];
typedef node* P_node;
P_node null,len,rot[maxn];
void Seg_T_init(){
	nil=node(0,0);null=&nil;
	null->l=null->r=null;len=base;
}
P_node newnode(int l,int r,P_node s1,P_node s2){
	*len=node(l,r);len->l=s1;len->r=s2;
	return len++;
}
P_node build(int l,int r){
	P_node x=newnode(l,r,null,null);
	if (l==r) return x;
	int mid=l+r>>1;
	x->l=build(l,mid);x->r=build(mid+1,r);
	x->pushup(); return x;
}
P_node ist(P_node lst,int k){
	P_node x=newnode(lst->L,lst->R,lst->l,lst->r);
	x->s=lst->s;
	if (x->L==x->R) {x->s++;return x;}
	int mid=x->L+x->R>>1;
	if (k<=mid) x->l=ist(lst->l,k);else
	 x->r=ist(lst->r,k);
	x->pushup(); return x;
}
int qry(P_node l,P_node r,int k){
	if (l->L==l->R) return r->s - l->s;
	int mid=l->L+l->R>>1;
	if (k<=mid) return qry(l->l,r->l,k);else return qry(l->r,r->r,k)+ r->l->s - l->l->s;
}
void print(P_node x){
	if (x==null) return;
	print(x->l);
	if (x->L==x->R) printf("%d ",x->s);
	print(x->r);
}
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot;
}
int main(){
	Seg_T_init();
	n=red();
	rot[0]=build(0,n);
	for (int i=1;i<=n;i++)
	 a[i]=red(),rot[i]=ist(rot[i-1],lst[a[i]]),lst[a[i]]=i;
	q=red();
	while (q--){
		int l=red(),r=red();
		printf("%d\n",qry(rot[l-1],rot[r],l-1));
	}
	return 0;
}
```

【莫队】
----
关于莫队[戳这里](http://blog.csdn.net/linkfqy/article/details/70833019)

这个就是典型的莫队题，没什么好讲的
不会莫队的可以看这里
复杂度$O(Q\sqrt n)$

附上代码：

```
#include<cstdio>
#include<cmath>
#include<algorithm>
using namespace std;
const int maxn=50005,maxq=200005;
int h[maxn],n,q,a[maxn],ans[maxq];
struct data{
	int l,r,id;
	bool operator<(const data&b)const{
		if (h[l]==h[b.l]) return r<b.r;
		return l<b.l;
	}
}que[maxq];
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
void blocker(){
	int k=sqrt(n);
	for (int i=1;i<=n;i++) h[i]=(i-1)/k+1;
}
int L=0,R=0,now=0,hsh[1000005];
void move(int x,int d){
	if (d==1){
		if (hsh[a[x]]==0) now++;
		hsh[a[x]]++;
	}else{
		hsh[a[x]]--;
		if (hsh[a[x]]==0) now--;
	}
}
int main(){
	n=red();
	for (int i=1;i<=n;i++) a[i]=red();
	blocker();
	q=red();
	for (int i=1;i<=q;i++) que[i].l=red(),que[i].r=red(),que[i].id=i;
	sort(que+1,que+1+q);
	for (int i=1;i<=q;i++){
		while (L<que[i].l) move(L++,-1);
		while (L>que[i].l) move(--L,1);
		while (R<que[i].r) move(++R,1);
		while (R>que[i].r) move(R--,-1);
		ans[que[i].id]=now;
	}
	for (int i=1;i<=q;i++) printf("%d\n",ans[i]);
	return 0;
}
```