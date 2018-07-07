[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1500)

![题面](http://img.blog.csdn.net/20170402113635171?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

这是Splay的终极模版题，思路非常清晰
这里提几个难点：

1.求最大子序列
我们可以对每个节点维护3个信息：
lm，rm，ma分别表示该段序列的最大前缀和、最大后缀和、最大子序列
那么maintain的时候就可以由子节点更新信息：
lm=max(s[0]->lm,s[0]->sum+x,s[0]->sum+x+s[1]->lm);
rm=max(s[1]->rm,s[1]->sum+x,s[1]->sum+x+s[0]->rm);
ma=max(s[0]->ma,s[1]->ma,s[0]->rm+x,s[1]->lm+x,x,s[0]->rm+x+s[1]->lm);
其实就是分别考虑是否取到左儿子，自己，右儿子
2.翻转与修改
显然，这里就需要打标记了，运用Lazy思想，
翻转的传递可以通过交换左右子树来完成
修改就可以强行修改，x，sum什么的都直接改掉
3.空间问题
由于操作次数较多，而题目又确定了数列的长度范围，我们可以使用new和delete操作来动态分配内存，这样就不会MLE了

大体上就是这样，要注意很多细节，比如插入和删除后别忘了maintain一下
贴上代码：

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
inline int red(){
    int tot=0,f=1;char ch=getchar();
    while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
    while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
    return tot*f;
}
int main(){
    splay_init();
    n=red(),q=red();
    for (int i=1;i<=n;i++) a[i]=red();
    rt=build(0,n+1);
    while (q--){
        scanf("%s",str);
        if (str[0]=='I'){
            int pos=red()+1,k=red();
            for (int i=1;i<=k;i++) a[i]=red();
            get_rec(rt,pos+1,pos);
            Rec=build(1,k);
            rt->s[1]->maintain();rt->maintain();
        }else
        if (str[0]=='D'){
            int l=red()+1,r=l+red()-1;
            get_rec(rt,l,r);
            recycle(Rec);Rec=null;
            rt->s[1]->maintain();rt->maintain();
        }else
        if (str[0]=='M')
         if (str[2]=='K'){
            int l=red()+1,r=l+red()-1;
            get_rec(rt,l,r);Rec->add_same(red());
         }else{
            get_rec(rt,2,rt->size-1);
            printf("%d\n",Rec->ma);
         }else
        if (str[0]=='R'){
            int l=red()+1,r=l+red()-1;
            get_rec(rt,l,r);Rec->add_flip();
        }else
        if (str[0]=='G'){
            int l=red()+1,r=l+red()-1;
            get_rec(rt,l,r);
            printf("%d\n",Rec->sum);
        }
    }
    return 0;
}
```