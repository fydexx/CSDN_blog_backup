#【二分+LCA差分乱搞】BZOJ4326（UOJ150） NOIP2015 运输计划

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=4326)

[还可以双倍经验](http://uoj.ac/problem/150)



其实就是要让用时最长的那条边最短

容易想到二分答案。

将所有长度大于答案的路径取交集，显然只能在这些边中选择一条来建造虫洞

当然是选择最长的那条边

然后差分乱搞一下就得到边集了

非常方便，不用打树链剖分



示例程序：

```C++
#include<cstdio>
#include<cmath>
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

const int maxn=300005,maxe=600005;
int n,m,tj,f[maxn][19],dep[maxn],dst[maxn];
int s[maxn],t[maxn],lca[maxn],length[maxn];
int tot,lnk[maxn],nxt[maxe],son[maxe],w[maxe];
inline void add(int x,int y,int wi){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;w[tot]=wi;
}
void getdep(int x,int fa){
	dep[x]=dep[fa]+1;f[x][0]=fa;
	for (int j=lnk[x];j;j=nxt[j])
	 if (fa!=son[j]) dst[son[j]]=dst[x]+w[j],getdep(son[j],x);
}
void DP(){
	for (int j=1;j<=tj;j++)
	 for (int i=1;i<=n;i++)
	  f[i][j]=f[f[i][j-1]][j-1];
}
inline int LCA(int a,int b){
	if (dep[a]<dep[b]) swap(a,b);
	for (int j=tj;j>=0;j--)
	 if (dep[f[a][j]]>=dep[b]) a=f[a][j];
	if (a==b) return a;
	for (int j=tj;j>=0;j--)
	 if (f[a][j]!=f[b][j]) a=f[a][j],b=f[b][j];
	return f[a][0];
}
int tag[maxn],S,max_edge;
int dfs(int x,int fa){
	int res=tag[x];
	for (int j=lnk[x];j;j=nxt[j])
	 if (fa!=son[j]){
	 	int tem=dfs(son[j],x);res+=tem;
	 	if (tem==S) max_edge=max(max_edge,w[j]);
	 }
	return res;
}
inline bool check(int maxs){
	cl(tag,0);S=0;max_edge=0;int maxl=0;
	for (int i=1;i<=m;i++)
	 if (length[i]>maxs) tag[s[i]]++,tag[t[i]]++,tag[lca[i]]-=2,S++,maxl=max(maxl,length[i]);
	dfs(1,0);
	return maxl-max_edge<=maxs;
}
int main(){
	n=red(),m=red();tj=log2(n);
	for (int i=1,x,y,z;i<n;i++) x=red(),y=red(),z=red(),add(x,y,z),add(y,x,z);
	getdep(1,0);f[1][0]=1;
	DP();
	int L=0,R=0,ans=-1;
	for (int i=1;i<=m;i++)
	 s[i]=red(),t[i]=red(),lca[i]=LCA(s[i],t[i]),
	 length[i]=dst[s[i]]+dst[t[i]]-dst[lca[i]]*2,R=max(R,length[i]);
	while (L<=R){
		int mid=L+R>>1;
		if (check(mid)) ans=mid,R=mid-1;else L=mid+1;
	}
	printf("%d",ans);
	return 0;
}
```

