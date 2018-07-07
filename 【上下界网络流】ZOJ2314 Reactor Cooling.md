[题面在这里](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemId=1314)

上下界网络流的裸题……

直接套用“无源汇有上下界可行流”即可

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=305,maxe=100005,INF=0x3f3f3f3f;
int tst,n,e,SS,TT,a[maxn],l[maxe],r[maxe];
int tot,lnk[maxn],nxt[maxe],son[maxe],flw[maxe],cap[maxe];
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
inline void add(int x,int y,int z){// printf("%d %d %d\n",x,y,z);
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;flw[tot]=0;cap[tot]=z;
	son[++tot]=x;nxt[tot]=lnk[y];lnk[y]=tot;flw[tot]=0;cap[tot]=0;
}
int d[maxn],que[maxn],pos[maxn];
bool bfs(int S,int T){
	memset(d,63,sizeof(d));
	int hed=0,til=1;
	que[1]=S;d[S]=0;
	while (hed!=til)
	 for (int j=lnk[que[++hed]];j;j=nxt[j])
	  if (d[son[j]]==INF&&flw[j]<cap[j])
	   que[++til]=son[j],d[son[j]]=d[que[hed]]+1;
	return d[T]!=INF;
}
int dfs(int x,int flow,int T){
	if (x==T||flow==0) return flow;
	int f,res=0;
	for (int &j=pos[x];j;j=nxt[j])
	 if (d[x]+1==d[son[j]]&&(f=dfs(son[j],min(flow,cap[j]-flw[j]),T))>0){
	 	flw[j]+=f;flw[j^1]-=f;
	 	res+=f;flow-=f;
	 	if (flow==0) break;
	 }
	return res;
}
void Dinic(){
	while (bfs(SS,TT)){
		memcpy(pos,lnk,sizeof(pos));
		dfs(SS,INF,TT);
	}
}
int main(){
	tst=red();
	while (tst--){
		n=red(),e=red();tot=1;SS=n+1;TT=n+2;
		memset(lnk,0,sizeof(lnk));
		memset(a,0,sizeof(a));
		for (int i=1,x,y;i<=e;i++)
		 x=red(),y=red(),l[i]=red(),r[i]=red(),add(x,y,r[i]-l[i]),a[x]-=l[i],a[y]+=l[i];
		for (int i=1;i<=n;i++)
		 if (a[i]>0) add(SS,i,a[i]);else add(i,TT,-a[i]);
		Dinic();
		bool suc=1;
		for (int i=1;i<=n;i++)
		 if(flw[(e+i)*2]<cap[(e+i)*2]) {suc=0;break;}
		if (suc){
			printf("YES\n");
			for (int i=1;i<=e;i++)
			 printf("%d\n",l[i]+flw[i*2]);
			putchar('\n');
		}else printf("NO\n\n");
	}
	return 0;
}
```