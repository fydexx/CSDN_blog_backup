很常见的字符串处理数据结构……
参考博客：[字符串统计神器——Trie树](http://blog.csdn.net/linkfqy/article/details/73065598)

以前没有怎么认真学过Trie，现在重新打了一遍
用了指针实现，感觉好看多了

```
#include<cstdio>
const int maxn=100005;
int n,q;
char s[maxn][15];
struct node{
    node *s[26];
    int cnt;
    node () {}
    node (int x):cnt(x) {}
}base[1200005],nil;
typedef node* P_node;
P_node null,len,Rot;
void Trie_init(){
    nil=node(0);null=&nil;len=base;
    for (int i=0;i<26;i++) null->s[i]=null;
}
P_node newnode(){
    *len=node(0);
    for (int i=0;i<26;i++) len->s[i]=null;
    return len++;
}
void ist(P_node &x,char *ch){
    if (x==null) x=newnode();
    x->cnt++;
    if (*ch==0) return;
    ist(x->s[*ch-'a'],ch+1);
}
int pre(P_node &x,char *ch){
    if (*ch==0) return x->cnt;
    return pre(x->s[*ch-'a'],ch+1);
}
int main(){
    Trie_init();Rot=newnode();
    //do something...
    return 0;
}
```