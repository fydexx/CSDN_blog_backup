[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1503)

Treap的经典题啊……


很典型的Treap，为了练手我用了FHQ_Treap
对于A和S操作，如果一个一个修改，显然是不行的
对于所有Treap中的元素，我们可以设置一个基准线，
所有人工资的真实值为Treap中的key+基准
那么对于A和S操作，我们只需修改基准即可

附上代码：

```
#include<cstdio>
#include<cstdlib>
const int maxn=100005;
struct node{
	node *s[2];
	int k,f,siz;
	node () {}
	node (int x,node *son) {k=x;f=rand();siz=1;s[0]=s[1]=son;}
	void pushup() {siz=s[0]->siz + s[1]->siz+1;}
}nil,base[maxn];
typedef node* P_node;
P_node null,len,Root;
void Treap_init(){
	nil=node(0,NULL);null=&nil;
	null->s[0]=null->s[1]=null; null->siz=0;
	len=base;Root=null;
}
P_node newnode(int x){
	*len=node(x,null);return len++;
}
P_node merge(P_node a,P_node b){
	if (a==null) return b;
	if (b==null) return a;
	if (a->f > b->f) {a->s[1]=merge(a->s[1],b);a->pushup();return a;}
	 b->s[0]=merge(a,b->s[0]);b->pushup();return b;
}
void split(P_node x,int k,P_node &l,P_node &r){
	if (x==null) {l=r=null;return;}
	if (x->k <= k) {l=x;split(l->s[1],k,l->s[1],r);}else
	 {r=x;split(r->s[0],k,l,r->s[0]);}
	x->pushup();
}
int kth(P_node x,int k){
	if (k<= x->s[0]->siz) return kth(x->s[0],k);
	if (k > x->s[0]->siz+1) return kth(x->s[1],k - x->s[0]->siz -1);
	return x->k;
}
void print(P_node x){
	if (x==null) return;
	print(x->s[0]);
	printf("%d ",x->k);
	print(x->s[1]);
}
int q,Min,sta,ans=0;
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int main(){
	Treap_init();
	q=red();Min=red();sta=0;
	while (q--){
		char c=getchar();while (c<'A'||'Z'<c) c=getchar();
		if (c=='I'){
			int k=red()-sta;if (k<Min-sta) continue;
			P_node l,r;	split(Root,k,l,r);
			Root=merge(l,merge(newnode(k),r));
		}else
		if (c=='A') sta+=red();else
		if (c=='S'){
			int k=red();P_node tem;
			sta-=k; split(Root,Min-sta-1,tem,Root);
			ans+=tem->siz;
		}else{
			int k=red();
			if (k>Root->siz) printf("-1\n");else
			printf("%d\n",kth(Root,Root->siz-k+1)+sta);
		}
	}
	printf("%d\n",ans);
	return 0;
}
```