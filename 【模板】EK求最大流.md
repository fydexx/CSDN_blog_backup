求解最大流问题的最简便方法
但是效率不是很理想

参考博客：[网络流-最大流](http://blog.csdn.net/linkfqy/article/details/72228945)

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=505,maxe=40005;
int n,e,que[maxn],fa[maxn],id[maxn],ans;
bool vis[maxn];
struct data{
	int son[maxe],nxt[maxe],lnk[maxn],tot;
	int flw[maxe],cap[maxe];
	void add(int x,int y,int w){
		son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
		flw[tot]=0;cap[tot]=w;
	}
}a;
inline int red(){
	int tot=0;char ch=getchar();
	while (ch<'0'||'9'<ch) ch=getchar();
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot;
}
bool bfs(){
	memset(vis,0,sizeof(vis));
	int hed=0,til=1;
	que[1]=1;vis[1]=1;
	while (hed!=til)
	 for (int j=a.lnk[que[++hed]];j;j=a.nxt[j])
	  if (!vis[a.son[j]]&&a.cap[j]>a.flw[j]){
	  	que[++til]=a.son[j];vis[a.son[j]]=1;
	  	fa[a.son[j]]=que[hed];id[a.son[j]]=j;
	  	if (a.son[j]==n) return 1;
	  }
	return 0;
}
int main(){
	n=red(),e=red();a.tot=1;
	for (int i=1,x,y,w;i<=e;i++)
	 x=red(),y=red(),w=red(),a.add(x,y,w),a.add(y,x,0);
	while (bfs()){
		int Min=0x3f3f3f3f;
		for (int x=n;x!=1;x=fa[x])
		 Min=min(Min,a.cap[id[x]]-a.flw[id[x]]);
		ans+=Min;
		for (int x=n;x!=1;x=fa[x])
		 a.flw[id[x]]+=Min,a.flw[id[x]^1]-=Min;
	}
	printf("%d",ans);
	return 0;
}
```