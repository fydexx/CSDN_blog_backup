何为Trie树？
--------
先容我吐槽一下这个数据结构的名字……
/ˈtriː/？/ˈtraɪ/？傻傻分不清楚

Trie树，又称字典树，是一种树形数据结构
被广泛用于字符串的统计

Trie树的构造
--------
Trie树节点的每个儿子都代表一个字母
那么就可以用某节点到根的路径来表示一个字符串
如下图：
![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/be/Trie_example.svg/400px-Trie_example.svg.png)
（这颗Trie树保存了8个字符串："A", "to", "tea", "ted", "ten", "i", "in", "inn"）
代码实现：
```
struct node{
	node *s[27];
	int cnt;
	node () {}
	node (int x):cnt(x) {}
};
```
cnt就表示子树下的字符串（结尾）个数

显然，根节点Rot是不代表任何字母的

Trie树的操作
--------
Trie树支持三个操作：插入，查询，删除（由于不常见，这里不再赘述）

插入操作很简单，直接按字符串一路走过去即可
代码实现：

```
void ist(P_node &x,char *ch){
	if (x==null) x=newnode();
	x->cnt++;
	if (*ch==0) return;
	ist(x->s[*ch-'a'],ch+1);
}
```

查询操作就更简单了，这里实现一种 返回前缀个数 的查询

```
int pre(P_node &x,char *ch){
	if (*ch==0) return x->cnt;
	return pre(x->s[*ch-'a'],ch+1);
}
```

例题
--
[hihoCoder1014](http://hihocoder.com/problemset/problem/1014)

```
#include<cstdio>
const int maxn=100005;
int n,q;
char s[maxn][15];
struct node{
	node *s[27];
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
	scanf("%d",&n);
	for (int i=1;i<=n;i++) scanf("%s",s[i]),ist(Rot,s[i]);
	scanf("%d",&q);
	for (int i=1;i<=q;i++){
		char str[15];scanf("%s",str);
		printf("%d\n",pre(Rot,str));
	}
	return 0;
}
```