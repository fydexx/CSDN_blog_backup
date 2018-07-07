# 【Tarjan】UOJ#146 【NOIP2015】信息传递

[题面在这里](http://uoj.ac/problem/146)



Tarjan刷最小环……



示例程序：

```C++
#include<cstdio>
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

const int maxn=200005;
int n,ans;
int tot,nxt[maxn],lnk[maxn],son[maxn];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
int dfn[maxn],low[maxn],stk[maxn],times;
bool instk[maxn];
void tarjan(int x){
	dfn[x]=low[x]=++times;
	stk[++stk[0]]=x;instk[x]=1;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!dfn[son[j]]) tarjan(son[j]),low[x]=min(low[x],low[son[j]]);else
	 if (instk[son[j]]) low[x]=min(low[x],dfn[son[j]]);
	if (low[x]==dfn[x]){
		int total=0;
		while (stk[stk[0]+1]!=x)
		 instk[stk[stk[0]--]]=0,total++;
		if (total>1) ans=min(ans,total);
	}
}
int main(){
	n=red();ans=1e9;
	for (int i=1,x;i<=n;i++) x=red(),add(i,x);
	for (int i=1;i<=n;i++)
	 if (!dfn[i]) tarjan(i);
	printf("%d",ans);
	return 0;
}
```

