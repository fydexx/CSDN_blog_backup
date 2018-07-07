[题面在这里](http://poj.org/problem?id=3207)

典型的2-SAT问题……

可以发现，对于任意两条连线，能在O(1)复杂度内判断是否矛盾
（即两条连线必须位于圆的两侧）
这样暴力枚举一下建边，用Tarjan刷强连通分量
最后判断是否有解即可

示例程序：

```
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn=1005,maxe=1000005;
int N,M,n;
int tot,lnk[maxn],nxt[maxe],son[maxe];
struct data{
	int s,t;
	bool check(int x) {return s<x&&x<t;}
}a[maxn];
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
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
int dfn[maxn],stk[maxn],low[maxn],to[maxn],tim;
bool instk[maxn];
void tarjan(int x){
	low[x]=dfn[x]=++tim;
	stk[++stk[0]]=x;instk[x]=1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!dfn[son[j]]) tarjan(son[j]),low[x]=min(low[x],low[son[j]]);else
	 if (instk[son[j]]) low[x]=min(low[x],dfn[son[j]]);
	if (low[x]==dfn[x]){
		while (stk[stk[0]+1]!=x)
		 instk[stk[stk[0]]]=0,to[stk[stk[0]--]]=x;
	}
}
int main(){
	N=red(),M=red();
	for (int i=1;i<=M;i++){
		a[i].s=red(),a[i].t=red();
		if (a[i].s>a[i].t) swap(a[i].s,a[i].t);
	}
	for (int i=1;i<M;i++)
	 for (int j=i+1;j<=M;j++)
	  if (a[i].check(a[j].s)^a[i].check(a[j].t))
	   add(i*2,j*2+1),add(i*2+1,j*2),add(j*2,i*2+1),add(j*2+1,i*2);
	n=M*2+1;
	for (int i=2;i<=n;i++)
	 if (!dfn[i]) tarjan(i);
	for (int i=2;i<=n;i++)
	 if (to[i]==to[i^1]) return printf("the evil panda is lying again"),0;
	return printf("panda is telling the truth..."),0;
}
```