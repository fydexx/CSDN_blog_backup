# 【DFS乱搞】UOJ#16 【NOIP2014】联合权值

[题面在这里](http://uoj.ac/problem/16)



没什么技巧，就是一个DFS乱搞的题目

距离为2的点只有2种：互为兄弟节点，深度相差2

所以维护$f_i$表示i的儿子的w之和

$g_i$表示i的儿子中最大的w

然后就很好统计答案了



示例程序：

```C++
#include<cstdio>
#include<algorithm>
typedef long long ll;
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

const int maxn=200005,tt=10007,maxe=400005,inv2=tt+1>>1;
int n,g[maxn],w[maxn],Max;
int tot,son[maxe],nxt[maxe],lnk[maxn];
ll ans,f[maxn];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
void dfs(int x,int fa){
	ll sum=0,res=0;int cd=0;
	for (int j=lnk[x];j;j=nxt[j])
	 if (son[j]!=fa){
	 	sum+=w[son[j]],dfs(son[j],x),
		(ans+=f[son[j]]*w[x])%=tt,
		Max=max(Max,g[son[j]]*w[x]);
		if (w[son[j]]>cd)
		 if (w[son[j]]>=g[x]) cd=g[x],g[x]=w[son[j]];else cd=w[son[j]];
	 }
	for (int j=lnk[x];j;j=nxt[j])
	 if (son[j]!=fa){
	 	(res+=w[son[j]]*(sum-w[son[j]]))%=tt;
	 	int t=w[son[j]]==g[x]?cd:g[x];
	 	Max=max(Max,t*w[son[j]]);
	 }
	(ans+=res*inv2)%tt;f[x]=sum;
}
int main(){
	n=red();
	for (int i=1,x,y;i<n;i++) x=red(),y=red(),add(x,y),add(y,x);
	for (int i=1;i<=n;i++) w[i]=red();
	dfs(1,0);
	printf("%d %lld",Max,ans*2%tt);
	return 0;
}
```

