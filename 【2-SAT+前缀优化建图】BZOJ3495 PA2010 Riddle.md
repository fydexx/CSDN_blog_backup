[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3495)

很典型的2-SAT问题……
可以对每个点是否为首都建图
考虑一条边(u,v)，如果u不是首都，则v必须是。反之亦然。
$u'\rightarrow v，v'\rightarrow u$
然后对于一个国家里的点，有一个是首都则其他都不是首都

但是这样边数是$N^2$级别的

可以用前缀优化建图。
对于一个点u，建2个变量
分别表示 u是否为首都（u与u'） 和 u所在的国家中以u为结尾的前缀是否存在首都（U与U'）
那么显然有以下限制：

 1. $u\rightarrow U，U'\rightarrow u'$
 2. $pre(U)\rightarrow U，U'\rightarrow pre(U)'$
 3. $u\rightarrow pre(U)'，pre(U)\rightarrow u'$

然后就好了

示例程序：

```
#include<cstdio>
#include<algorithm>
using namespace std;
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res*f;
}

const int maxn=4000010,maxe=8000005;
int n,e,m,pre[maxn];
int tot,lnk[maxn],son[maxe],nxt[maxe];
inline void add(int x,int y){// printf("%d %d\n",x,y); 
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
int dfn[maxn],low[maxn],stk[maxn],scc[maxn],Tim;
bool instk[maxn];
void tarjan(int x){
	stk[++stk[0]]=x;instk[x]=1;
	dfn[x]=low[x]=++Tim;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!dfn[son[j]]) tarjan(son[j]),low[x]=min(low[x],low[son[j]]);else
	 if (instk[son[j]]) low[x]=min(low[x],dfn[son[j]]);
	if (low[x]==dfn[x]){
		scc[0]++;
		while (stk[stk[0]+1]!=x)
		 instk[stk[stk[0]]]=0,scc[stk[stk[0]--]]=scc[0];
	}
}
int main(){
	n=red();e=red(),m=red();
	for (int i=1,x,y;i<=e;i++)
	 x=red(),y=red(),add(4*x+1,4*y),add(4*y+1,4*x);
	for (int i=1;i<=m;i++){
		int k=red(),lst=red();
		for (int j=1,x;j<k;j++) x=red(),pre[x]=lst,lst=x;
	}
	for (int i=1;i<=n;i++){
		add(4*i,4*i+2),add(4*i+3,4*i+1);
		if (pre[i]){
			int j=pre[i];
			add(4*j+2,4*i+2);add(4*i+3,4*j+3);
			add(4*j+2,4*i+1);add(4*i,4*j+3);
		}
	}
	Tim=0;int N=4*n+3;
	for (int i=4;i<=N;i++)
	 if (!dfn[i]) tarjan(i);
	for (int i=4;i<=N;i++)
	 if (scc[i]==scc[i^1]) return printf("NIE"),0;
	printf("TAK");
	return 0;
}
```