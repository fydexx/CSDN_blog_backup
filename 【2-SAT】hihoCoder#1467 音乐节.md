[题面在这里](http://hihocoder.com/problemset/problem/1467)

2-SAT的最经典应用……

不说了，直接上代码：

```
#include<cstdio>
#include<cstring>
#define getw(x) ((x)?0:n)
const int maxn=205,maxe=2005;
int tst,n,m;
int tot=0,lnk[maxn],nxt[maxe],son[maxe];
bool vis[maxn];
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
inline char fstchar(){
	char ch=nc();while (ch<'a'||'z'<ch) ch=nc();
	return ch;
}
inline void add(int x,int y){//printf("%d %d\n",x,y);
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
bool dfs(int x,int t){
	if (x==t) return 1;vis[x]=1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!vis[son[j]]&&dfs(son[j],t)) return 1; 
	return 0;
}
int main(){
	tst=red();
	while (tst--){
		tot=0;memset(lnk,0,sizeof(lnk));
		n=red(),m=red();
		for (int i=1,x,y;i<=m;i++){
			bool a,b;
			a=(fstchar()=='m');x=red();b=(fstchar()=='m');y=red();
			add(getw(!a)+x,getw(b)+y);add(getw(!b)+y,getw(a)+x);
		}
		bool suc=1;
		for (int i=1;i<=n;i++)
		 if (memset(vis,0,sizeof(vis)),dfs(i,i+n)&&memset(vis,0,sizeof(vis)),dfs(i+n,i)) {suc=0;break;}
		if (suc) printf("GOOD\n");else printf("BAD\n");
	}
	return 0;
}
```