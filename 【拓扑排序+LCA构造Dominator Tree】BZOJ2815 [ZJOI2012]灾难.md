[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2815)

对每一个生产者建立一个共同的食物（太阳233）
可以对整个图拓扑排序
然后按倒拓扑序构造Dominator Tree
显然，对于一个点，它的所有食物的LCA就是其在Dominator Tree上的father
然后就好了

示例程序：

```
#include<cstdio>
#include<cmath>
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

const int maxn=65500,maxe=1000005;
int n,g[maxn],f[maxn][17],dep[maxn],w[maxn];
int tot,lnk[maxn],nxt[maxe],son[maxe];
inline void add(int x,int y){ g[y]++;
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
int que[maxn];
void topo(){
	int hed=0,til=0;
	for (int i=1;i<=n;i++)
	 if (!g[i]) que[++til]=i;
	while (hed!=til)
	 for (int j=lnk[que[++hed]];j;j=nxt[j]){
	 	g[son[j]]--;
	 	if (!g[son[j]]) que[++til]=son[j];
	 }
}
int LCA(int a,int b){
	if (dep[a]<dep[b]) swap(a,b);
	for (int j=log2(n+1);j>=0;j--)
	 if (dep[f[a][j]]>=dep[b]) a=f[a][j];
	if (a==b) return a;
	for (int j=log2(n+1);j>=0;j--)
	 if (f[a][j]!=f[b][j]) a=f[a][j],b=f[b][j];
	return f[a][0];
}
int main(){
	n=red();
	for (int i=1;i<=n;i++)
	 for (int x=red();x;x=red()) add(i,x);
	topo();
	for (int i=0;(1<<i)<=n+1;i++) f[n+1][i]=n+1;dep[n+1]=1;
	for (int i=n;i>=1;i--){
		int x=-1;
		if (!lnk[que[i]]) x=n+1;
		for (int j=lnk[que[i]];j;j=nxt[j])
		 if (x<0) x=son[j];else x=LCA(x,son[j]);
		f[que[i]][0]=x;dep[que[i]]=dep[x]+1;
		for (int j=1;(1<<j)<=n+1;j++)
		 f[que[i]][j]=f[f[que[i]][j-1]][j-1];
	}
	for (int i=1;i<=n;i++) w[f[que[i]][0]]+=w[que[i]]+1;
	for (int i=1;i<=n;i++) printf("%d\n",w[i]);
	return 0;
} 
```