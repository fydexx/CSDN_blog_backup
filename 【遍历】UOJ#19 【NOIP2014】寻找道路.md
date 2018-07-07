# 【遍历】UOJ#19 【NOIP2014】寻找道路

[题面在这里](http://uoj.ac/problem/19)



直接先处理出那些点不能走，然后乱搞就好了



示例程序：

```C++
#include<cstdio>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))
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

const int maxn=10005,maxe=200005;
int n,e,S,T;
bool vis[maxn],can[maxn];
namespace A{
	int tot,nxt[maxe],lnk[maxn],son[maxe];
	inline void add(int x,int y){
		son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
	} 
}
namespace B{
	int tot,nxt[maxe],lnk[maxn],son[maxe];
	inline void add(int x,int y){
		son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
	} 
}
void dfs(int x){
	using namespace B;
	vis[x]=1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!vis[son[j]]) dfs(son[j]);
}
int que[maxn],d[maxn];
void bfs(){
	using namespace A;
	cl(vis,0);cl(d,255);
	if (!can[S]) return;
	int hed=0,til=1;
	que[1]=S;vis[S]=1;d[S]=0;
	while (hed!=til)
	 for (int j=lnk[que[++hed]];j;j=nxt[j])
	  if (!vis[son[j]]&&can[son[j]])
	   que[++til]=son[j],vis[son[j]]=1,d[son[j]]=d[que[hed]]+1;
}
int main(){
	n=red(),e=red();
	for (int i=1,x,y;i<=e;i++) x=red(),y=red(),A::add(x,y),B::add(y,x);
	S=red(),T=red();
	dfs(T);
	for (int i=1;i<=n;i++){
		int suc=1;
		for (int j=A::lnk[i];j;j=A::nxt[j])
		 if (!vis[A::son[j]]) {suc=0;break;}
		if (suc) can[i]=1;
	}
	bfs();
	printf("%d",d[T]);
	return 0;
} 
```

