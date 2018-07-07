[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2199)

2-SAT经典题……

没什么好说的，直接上代码吧

```
#include<cstdio>
#include<cstring>
#define cl(x) memset(x,0,sizeof(x))
const int maxn=2005,maxe=8005;
int N,M,n;
int tot,lnk[maxn],nxt[maxe],son[maxe];
bool vis[maxn],ans[maxn][2];
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
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
bool dfs(int x,int t){
	if (vis[x]) return 0;
	vis[x]=1;
	if (x==t) return 1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (dfs(son[j],t)) return 1;
	return 0;
}
int main(){
	N=red(),M=red();n=N*2+1;
	for (int i=1;i<=M;i++){
		int x,y;bool a,b;
		x=red(),a=nc()=='Y',y=red(),b=nc()=='Y';
		add(x*2+!a,y*2+b);add(y*2+!b,x*2+a);
	}
	for (int i=1;i<=N;i++){
		int j=i*2;cl(vis);
		if (!dfs(j,j^1)) ans[i][0]=1;
		cl(vis);
		if (!dfs(j^1,j)) ans[i][1]=1;
		if (!(ans[i][0]||ans[i][1])) return printf("IMPOSSIBLE"),0;
	}
	for (int i=1;i<=N;i++)
	 if (ans[i][0]&&ans[i][1]) putchar('?');else
	 if (ans[i][0]) putchar('N');else putchar('Y');
	return 0;
}
```