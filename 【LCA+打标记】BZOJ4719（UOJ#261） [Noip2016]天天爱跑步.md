# 【LCA+打标记】BZOJ4719（UOJ#261） [Noip2016]天天爱跑步

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=4719)

[还可以双倍经验](http://uoj.ac/problem/261)



不得不说真是一道好题啊……

对于路径$x\rightarrow y$，可以拆分为$x\rightarrow lca$和$lca\rightarrow y$

同时注意到，对于点i在一条往上走的路径$x\rightarrow y$上：

$$
deep_i+w_i=deep_x
$$

反之：
$$
deep_i-w_i=deep_y-dist_{x,y}
$$
维护一个两个桶$up,down$，那么某个点x的答案就是$up[deep_i+w_i]+down[deep_i-w_i]$

然后就可以打标记了：对x,y分别打+标记，lca打两个-标记

DFS时遇到标记就在桶里增加值

注意要使打的标记只在对应的路径上生效，实际上应该记录x入栈前与入栈后桶的差值



还有，LCA的答案有可能算重复，需要注意一下



示例程序：

```C++
#include<cstdio>
#include<cmath>
#include<vector>
#define vit vector<int>::iterator
#define pb push_back
#include<algorithm>
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
int n,q,lgn,up[maxe],down[maxe],ans[maxn],w[maxn];
int tot,son[maxe],nxt[maxe],lnk[maxn];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
int dep[maxn],f[maxn][20];
void getdep(int x,int fa){
	f[x][0]=fa;dep[x]=dep[fa]+1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (son[j]!=fa) getdep(son[j],x);
}
void DP(){
	for (int j=1;j<=lgn;j++)
	 for (int i=1;i<=n;i++)
	  f[i][j]=f[f[i][j-1]][j-1];
}
inline int LCA(int x,int y){
	if (dep[x]<dep[y]) swap(x,y);
	for (int j=lgn;j>=0;j--)
	 if (dep[f[x][j]]>=dep[y]) x=f[x][j];
	if (x==y) return x;
	for (int j=lgn;j>=0;j--)
	 if (f[x][j]!=f[y][j]) x=f[x][j],y=f[y][j];
	return f[x][0];
}
vector<int> a[2][maxn],b[2][maxn];
void dfs(int x,int fa){
	int lu=up[dep[x]+w[x]],ld=down[dep[x]-w[x]+300000];
	for (int j=lnk[x];j;j=nxt[j])
	 if (son[j]!=fa) dfs(son[j],x);
	for (vit it=a[0][x].begin();it!=a[0][x].end();it++)
	 up[*it]++;
	for (vit it=b[0][x].begin();it!=b[0][x].end();it++)
	 down[*it]++;
	ans[x]+=up[dep[x]+w[x]]-lu + down[dep[x]-w[x]+300000]-ld; 
	for (vit it=a[1][x].begin();it!=a[1][x].end();it++)
	 up[*it]--;
	for (vit it=b[1][x].begin();it!=b[1][x].end();it++)
	 down[*it]--;
}
int main(){
	n=red(),q=red();lgn=log2(n);
	for (int i=1,x,y;i<n;i++) x=red(),y=red(),add(x,y),add(y,x);
	getdep(1,1);DP();
	for (int i=1;i<=n;i++) w[i]=red();
	while (q--){
		int x=red(),y=red(),lca=LCA(x,y),t=dep[x]+dep[y]-2*dep[lca];
		a[1][lca].pb(dep[x]),a[0][x].pb(dep[x]),
		b[1][lca].pb(dep[y]-t+300000),b[0][y].pb(dep[y]-t+300000);
		if (w[lca]==dep[x]-dep[lca]) ans[lca]--;
	}
	dfs(1,0);
	for (int i=1;i<n;i++) printf("%d ",ans[i]);printf("%d",ans[n]);
	return 0;
}
```

