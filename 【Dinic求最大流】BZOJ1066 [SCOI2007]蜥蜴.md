[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1066)

又是一道网络流题

这种题型还需要多练习啊……减少思考建模的时间……

不难发现，可以把蜥蜴看做流，每根石柱都是点
曼哈顿距离不超过d的点之间可以建边
但是每个石柱能经过的蜥蜴只数有限制，那么就拆点建边
最后建立超级源点和超级汇点即可。

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=905,maxe=320005,INF=0x3f3f3f3f;
int a,b,l,S,T,n,num,ans=0;
int lnk[maxn],flw[maxe],cap[maxe],son[maxe],nxt[maxe],tot=1;
int pos[maxn],d[maxn],que[maxn];
inline char fstchar(){
	char ch=getchar();
	while ((ch<'0'||'9'<ch)&&ch!='L'&&ch!='.') ch=getchar();
	return ch;
}
inline int abs(int x){return x>0?x:-x;}
inline int getid(int x,int y,int t){
	return (b*(x-1)+y)*2-t;
}
void add(int x,int y,int f){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;flw[tot]=0;cap[tot]=f;
	son[++tot]=x;nxt[tot]=lnk[y];lnk[y]=tot;flw[tot]=0;cap[tot]=0;
}
bool bfs(){
	memset(d,63,sizeof(d));
	int hed=0,til=1;
	que[1]=S;d[S]=0;
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
	 }
	return res;
}
int main(){
	scanf("%d%d%d",&a,&b,&l);
	n=a*b*2+1;S=0,T=n;
	for (int i=1;i<=a;i++)
	 for (int j=1;j<=b;j++)
	  add(getid(i,j,0),getid(i,j,1),fstchar()-48);
	for (int i=1;i<=a;i++)
	 for (int j=1;j<=b;j++){
	 	if (fstchar()=='L') add(S,getid(i,j,0),1),num++;
	 	if (i<=l||j<=l||a-i<l||b-j<l) add(getid(i,j,1),T,INF);
	 }
	for (int i=1;i<=a;i++)
	 for (int j=1;j<=b;j++)
	  for (int ii=1;ii<=a;ii++)
	   for (int jj=1;jj<=b;jj++)
	    if (abs(i-ii)+abs(j-jj)<=l) add(getid(i,j,1),getid(ii,jj,0),INF);
	while (bfs()){
		memcpy(pos,lnk,sizeof(lnk));
		ans+=dfs(S,INF);
	}
	printf("%d",num-ans);
	return 0;
}
```