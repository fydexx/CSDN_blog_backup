[题面在这里](http://hihocoder.com/problemset/problem/1468)

典型的2-SAT问题

此题只能用DFS暴力乱搞，因为要输出字典序最小的一组解

示例程序：

```
#include<cstdio>
const int maxn=16005,maxe=40005;
int n,e;
int tot,lnk[maxn],nxt[maxe],son[maxe];
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
bool vis[maxn];
int stk[maxn];
bool dfs(int x,int t){
	stk[++stk[0]]=x;vis[x]=1;
	if (x==t) return 1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!vis[son[j]]&&dfs(son[j],t)) return 1;
	return 0;
}
int main(){
	n=red(),e=red();
	for (int i=1,x,y;i<=e;i++)
	 x=red()+1,y=red()+1,add(x,y^1),add(y,x^1);
	for (int i=2;i<=2*n;i+=2){
		if (vis[i]||vis[i^1]) continue;
		stk[0]=0;
		if (dfs(i,i^1)){
			while (stk[0]) vis[stk[stk[0]--]]=0;
			if (dfs(i^1,i)) return printf("NIE"),0;
		}
	}
	for (int i=2;i<=2*n+1;i++) if (vis[i]) printf("%d\n",i-1);
	return 0;
}
```