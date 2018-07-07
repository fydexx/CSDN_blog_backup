[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1997)

因为平面图有一个性质，就是边数不超过3N-6
所以可以首先确定边数大于3N-6的case了
那么边数就成了$O(n)$级别的了
然后是典型的2-SAT问题：
每条边要么在哈密顿回路的内侧，要么在外侧
Tarjan缩点判定即可

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define cl(x,y) memset(x,y,sizeof(x))
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

const int maxn=1205,maxe=720005;
int tst,n,e,X[maxn],Y[maxn],v[maxn],b[maxn];
int tot,nxt[maxe],lnk[maxn],son[maxe];
inline void add(int x,int y){// printf("%d %d\n",x,y);
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
bool check(int i,int x){
	return v[X[i]]<x&&x<v[Y[i]];
}
int dfn[maxn],stk[maxn],low[maxn],Tim,scc[maxn];
bool instk[maxn],vis[maxn][maxn];
void tarjan(int x){
	dfn[x]=low[x]=++Tim;
	stk[++stk[0]]=x;instk[x]=1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!dfn[son[j]]) tarjan(son[j]),low[x]=min(low[x],low[son[j]]);else
	 if (instk[son[j]]) low[x]=min(low[x],dfn[son[j]]);
	if (dfn[x]==low[x]){
		scc[0]++;
		while (stk[stk[0]+1]!=x)
		 instk[stk[stk[0]]]=0,scc[stk[stk[0]--]]=scc[0];
	}
}
int main(){
	tst=red();
	while (tst--){
		n=red(),e=red();
		for (int i=1;i<=e;i++) X[i]=red(),Y[i]=red();
		for (int i=1;i<=n;i++) b[i]=red(),v[b[i]]=i;
		if (e>3*n-6) {printf("NO\n");continue;}
		cl(vis,0);vis[b[1]][b[n]]=vis[b[n]][b[1]]=1;
		for (int i=2;i<=n;i++) vis[b[i]][b[i-1]]=vis[b[i-1]][b[i]]=1;
		for (int i=1;i<=e;i++)
		 if (v[X[i]]>v[Y[i]]) swap(X[i],Y[i]);
		cl(lnk,0);tot=0;
		for (int i=1;i<=e;i++)
		 if (!vis[X[i]][Y[i]])
		 for (int j=1;j<i;j++)
		  if (!vis[X[j]][Y[j]])
		  if ((check(i,v[X[j]])^check(i,v[Y[j]]))&&(check(j,v[X[i]])^check(j,v[Y[i]])))
		   add(i<<1,j<<1|1),add(i<<1|1,j<<1),add(j<<1,i<<1|1),add(j<<1|1,i<<1);//, printf("%d %d\n",i,j);
		int N=e<<1|1;
		cl(dfn,0);cl(instk,0);stk[0]=Tim=scc[0]=0;
		for (int i=2;i<=N;i++)
		 if (!dfn[i]) tarjan(i);
		bool suc=1;
		for (int i=2;i<=N;i++)
		 if (scc[i]==scc[i^1]) {suc=0;break;}
		if (suc) printf("YES\n");else printf("NO\n");
	}
	return 0;
} 
```