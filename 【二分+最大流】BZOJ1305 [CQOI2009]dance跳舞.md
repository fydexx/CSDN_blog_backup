#【二分+最大流】BZOJ1305 [CQOI2009]dance跳舞

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1305)



考虑把每个人拆成自己$x_i$和不喜欢自己$y_i$

如果$i$喜欢$j$，那么连边$(x_i,x_j,1)$，否则连边$(y_i,y_j,1)$

最多和讨厌的人跳K次舞，连边$(x_i,y_i,k)$

最后验证$(S,x_i,mid)$是否满流即可



示例程序：

```C++
#include<cstdio>
#include<algorithm>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))
using namespace std;

const int maxn=205,maxe=100005,INF=0x3f3f3f3f;
int n,K,S,T;
bool l[maxn][maxn];
int tot,nxt[maxe],lnk[maxn],flw[maxe],cap[maxe],son[maxe];
inline void add(int x,int y,int z){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;flw[tot]=0;cap[tot]=z;
	son[++tot]=x;nxt[tot]=lnk[y];lnk[y]=tot;flw[tot]=0;cap[tot]=0;
}
inline char fstchar(){
	char ch=getchar();
	while (ch<'A'||'Z'<ch) ch=getchar();
	return ch;
}
int que[maxn],d[maxn],pos[maxn];
bool bfs(){
	cl(d,255);
	int hed=0,til=1;
	que[1]=S;d[S]=0;
	while (hed!=til){
		int x=que[++hed];
		for (int j=lnk[x];j;j=nxt[j])
		 if (d[son[j]]<0&&flw[j]<cap[j])
		  d[son[j]]=d[x]+1,que[++til]=son[j];
	}
	return d[T]>0;
}
int dfs(int x,int flow){
	if (x==T||flow==0) return flow;
	int f,res=0;
	for (int &j=pos[x];j;j=nxt[j])
	 if (d[x]+1==d[son[j]]&&(f=dfs(son[j],min(flow,cap[j]-flw[j])))>0){
		flw[j]+=f;flw[j^1]-=f;
		res+=f;flow-=f;
		if (flow==0) break;
	 }
	return res;
}
int Dinic(){
	int ans=0;
	while (bfs()){
		memcpy(pos,lnk,sizeof(lnk));
		ans+=dfs(S,INF);
	}
	return ans;
}
bool check(int mid){
	S=4*n+1,T=4*n+2;
	cl(lnk,0);tot=1;
	for (int i=1;i<=n;i++)
	 add(S,i,mid),add(n*3+i,T,mid),add(i,n+i,K),add(2*n+i,3*n+i,K);
	for (int i=1;i<=n;i++)
	 for (int j=1;j<=n;j++)
	  if (l[i][j]) add(i,n*3+j,1);else add(n+i,n*2+j,1);
	return Dinic()==n*mid;
} 
int main(){
	scanf("%d%d",&n,&K);
	for (int i=1;i<=n;i++)
	 for (int j=1;j<=n;j++)
	  l[i][j]=(fstchar()=='Y');
	int L=0,R=n,ans=0;
	while (L<=R){
		int mid=L+R>>1;
		if (check(mid)) ans=mid,L=mid+1;else R=mid-1;
	}
	printf("%d",ans);
	return 0;
}
```

