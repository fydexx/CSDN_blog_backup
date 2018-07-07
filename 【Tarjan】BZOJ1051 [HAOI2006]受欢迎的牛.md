# 【Tarjan】BZOJ1051 [HAOI2006]受欢迎的牛

[题目在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1051)



很显然，缩点后无出度的强连通分量就是答案



示例程序：

```C++
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

const int maxn=10005,maxe=50005;
int n,e;
int tot,son[maxe],nxt[maxe],lnk[maxn];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
int stk[maxn],dfn[maxn],low[maxn],times,num[maxn],to[maxn],f[maxn];
bool instk[maxn],vis[maxn];
void tarjan(int x){
	low[x]=dfn[x]=++times;
	stk[++stk[0]]=x;instk[x]=1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!dfn[son[j]]) tarjan(son[j]),low[x]=min(low[x],low[son[j]]);else
	 if (instk[son[j]]) low[x]=min(low[x],dfn[son[j]]);
	if (low[x]==dfn[x]){
		while (stk[stk[0]+1]!=x)
		 num[x]++,to[stk[stk[0]]]=x,
		 instk[stk[stk[0]--]]=0;
	}
}
int main(){
	n=red(),e=red();
	for (int i=1,x,y;i<=e;i++) x=red(),y=red(),add(x,y);
	for (int i=1;i<=n;i++)
	 if (!dfn[i]) tarjan(i);
	for (int i=1;i<=n;i++)
	 for (int j=lnk[i];j;j=nxt[j])
	  if (to[i]!=to[son[j]]) f[to[i]]++;
	int ans,tal=0;
	for (int i=1;i<=n;i++)
	 if (num[i]&&!f[i]) ans=num[i],tal++;
	if (tal>1) printf("0");else printf("%d",ans);
	return 0;
}
```

