简单的用SPFA增广进行费用流的求解
与EK求最大流类似，只是此时要求最大流的同时费用最小
所以用SPFA增广，就可以费用尽量小

模板：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=705,maxe=144005,INF=0x3f3f3f3f;
int N,M,S,T,n,tim[65][15],id[65][15],ans=0;
int tot=1,son[maxe],nxt[maxe],lnk[maxn],flw[maxe],cap[maxe],w[maxe];
void add(int x,int y,int z){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;flw[tot]=0;cap[tot]=1;w[tot]=z;
	son[++tot]=x;nxt[tot]=lnk[y];lnk[y]=tot;flw[tot]=0;cap[tot]=0;w[tot]=-z;
}
#define nc getchar
inline int red(){
	int tot=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=nc();
	return tot*f;
}
int que[maxn],dst[maxn],fa[maxn],ed[maxn];
bool vis[maxn];
bool spfa(){
	memset(dst,63,sizeof(dst));
	memset(vis,0,sizeof(vis));
	int hed=0,til=1;
	que[1]=S;dst[S]=0;fa[S]=0;
	while (hed!=til){
		int x=que[hed=(hed+1)%maxn];
		vis[x]=0;
		for (int j=lnk[x];j;j=nxt[j])
		 if (cap[j]>flw[j]&&dst[son[j]]>dst[x]+w[j]){
		 	dst[son[j]]=dst[x]+w[j];
		 	fa[son[j]]=x;ed[son[j]]=j;
		 	if (!vis[son[j]])
		 	 vis[son[j]]=1,
		 	 que[til=(til+1)%maxn]=son[j];
		 }
	}
	if (dst[T]==INF) return 0;
	ans+=dst[T];return 1;
}
int main(){
	//do something...
	while (spfa()){
		int Min=INF;
		for (int j=T;j!=S;j=fa[j]) Min=min(Min,cap[ed[j]]-flw[ed[j]]);
		for (int j=T;j!=S;j=fa[j]) flw[ed[j]]+=Min,flw[ed[j]^1]-=Min;
	}
	printf("%.2lf",(double)ans/N);
	return 0;
}
```