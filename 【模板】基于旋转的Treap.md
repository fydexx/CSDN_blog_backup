Treap作为一种典型的平衡树，
能实现插入、删除、查找等操作
初学者必学！

关于Treap，[戳这里](http://blog.csdn.net/linkfqy/article/details/60468010)

写得较为简洁：
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