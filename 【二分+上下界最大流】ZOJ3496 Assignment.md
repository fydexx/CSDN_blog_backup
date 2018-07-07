[题面在这里](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemId=4319)

首先要明确，B公司肯定会把流量最大（小）的边代价设为P，其他代价为0（根据贪心原理）。
然后就是要使 流量最大的边最小（或流量最小的边最大）
二分枚举答案，然后就确定了每条边的流量范围
妥妥的上下界最大流

**注意：如果二分确定的范围不在一条边原有的流量范围内，就验证失败**
（因为流量取不到当前的值）
这个错误很容易被忽略，我因此WA了一个下午……

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define LL long long
#define del_e(x) flw[x]=cap[x]=flw[x^1]=cap[x^1]=0
using namespace std;
const int maxn=605,maxe=40005,INF=0x3f3f3f3f;
int tst,n,e,S,T,P,maxflow;
int tot,son[maxe],nxt[maxe],lnk[maxn],cap[maxe],flw[maxe];
#define nc getchar
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res*f;
}
struct edge{
	int x,y,z;
	void read() {x=red()+1,y=red()+1,z=red();}
}a[maxe];
void add(int x,int y,int z){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;cap[tot]=z;flw[tot]=0;
	son[++tot]=x;nxt[tot]=lnk[y];lnk[y]=tot;cap[tot]=0;flw[tot]=0;
}
int que[maxn],d[maxn],pos[maxn];
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
	if (flow==0||x==T) return flow;
	int res=0,f;
	for (int &j=pos[x];j;j=nxt[j])
	 if (d[son[j]]==d[x]+1&&(f=dfs(son[j],min(flow,cap[j]-flw[j]),T))>0){
	 	flw[j]+=f;flw[j^1]-=f;
	 	flow-=f;res+=f;
	 	if (flow==0) break;
	 }
	return res;
}
int Dinic(int S,int T){
	int res=0;
	while (bfs(S,T)){
		memcpy(pos,lnk,sizeof(lnk));
		res+=dfs(S,INF,T);
	}
	return res;
}
int tmp[maxn];
inline void Del(int x){
	for (int j=lnk[x];j;j=nxt[j]) del_e(j);
}
bool check(int L,int R){
	tot=1;memset(lnk,0,sizeof(lnk));
	memset(tmp,0,sizeof(tmp));
	int SS=n+1,TT=n+2;
	for (int i=1;i<=e;i++){
		int l=L,r=min(R,a[i].z);
		if (l>r) return 0;
		tmp[a[i].x]-=l;tmp[a[i].y]+=l;
		add(a[i].x,a[i].y,r-l);
	}
	int blc=0;
	for (int i=1;i<=n;i++)
	 if (tmp[i]>0) add(SS,i,tmp[i]),blc+=tmp[i];else add(i,TT,-tmp[i]);
	add(T,S,INF);
	if (Dinic(SS,TT)<blc) return 0;
	int res=flw[tot^1];
	del_e(tot);Del(SS);Del(TT);
	return res+Dinic(S,T)==maxflow;
}
int main(){
	tst=red();
	while (tst--){
		n=red(),e=red(),S=red()+1,T=red()+1,P=red();
		tot=1;memset(lnk,0,sizeof(lnk));
		for (int i=1;i<=e;i++) a[i].read(),add(a[i].x,a[i].y,a[i].z);
		if (e==0) {printf("0 0\n");continue;}
		maxflow=Dinic(S,T);
		
		int L=0,R=100000,ans;
		while (L<=R){
			int mid=L+R>>1;
			if (check(0,mid)) R=mid-1,ans=mid;else L=mid+1;
		}
		printf("%lld ",(LL)ans*P);
		L=0,R=100000,ans=0;
		while (L<=R){
			int mid=L+R>>1;
			if (check(mid,INF)) L=mid+1,ans=mid;else R=mid-1;
		}
		printf("%lld\n",(LL)ans*P);
	}
	return 0;
}
```