求最大流的常规方法
使用层次图思想，一次增广多条路，效率大大提高

参考博客：[网络流-最大流](http://blog.csdn.net/linkfqy/article/details/72228945)

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=505,maxe=40004,INF=0x3f3f3f3f;
int n,e,S,T;
int tot,lnk[maxn],nxt[maxe],cap[maxe],flw[maxe],son[maxe];
void add(int x,int y,int w){
	son[++tot]=y;cap[tot]=w;flw[tot]=0;nxt[tot]=lnk[x];lnk[x]=tot;
}
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int d[maxn],pos[maxn],que[maxn];
bool bfs(){
	memset(d,63,sizeof(d));
	int hed=0,til=1;
	d[S]=0;que[1]=S;
	while (hed!=til)
	 for (int j=lnk[que[++hed]];j;j=nxt[j])
	  if (d[son[j]]==INF&&flw[j]<cap[j])
	   que[++til]=son[j],d[son[j]]=d[que[hed]]+1;
	return d[T]!=INF;
}
int dfs(int x,int flow){
	if (x==T||flow==0) return flow;
	int res=0,f;
	for (int &j=pos[x];j;j=nxt[j])
	 if (d[x]+1==d[son[j]]&&(f=dfs(son[j],min(flow,cap[j]-flw[j])))>0){
	 	flw[j]+=f;flw[j^1]-=f;
	 	res+=f;flow-=f;
	 	if (flow==0) break;
	 }
	return res;
}
int main(){
	n=red(),e=red();S=1;T=n;tot=1;
	for (int i=1,x,y,z;i<=e;i++)
	 x=red(),y=red(),z=red(),add(x,y,z),add(y,x,0);
	int ans=0;
	while (bfs()){
		memcpy(pos,lnk,sizeof(lnk));
		ans+=dfs(S,INF);
	}
	printf("%d",ans);
	return 0;
}
```