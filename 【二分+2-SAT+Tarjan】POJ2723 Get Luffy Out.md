[题面在这里](http://poj.org/problem?id=2723)

2-SAT经典题……

钥匙只能二选一，所以很容易看出是2-SAT问题
考虑约束关系：
对于一扇门，假设它两把锁为(a,b)
那么为了过这扇门，选了a'就一定要选b，反之亦然
后面不需要讲了……

然而每次枚举过几扇门太慢了，直接二分答案

注意：数据范围有点坑

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=5000,maxe=maxn;
int N,M,n,p[maxn];
int tot,lnk[maxn],son[maxe],nxt[maxe];
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
struct door{
	int x,y;
}a[maxe];
int dfn[maxn],low[maxn],stk[maxn],to[maxn],tim;
bool instk[maxn];
void tarjan(int x){
	stk[++stk[0]]=x;instk[x]=1;
	dfn[x]=low[x]=++tim;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!dfn[son[j]]) tarjan(son[j]),low[x]=min(low[x],low[son[j]]);else
	 if (instk[son[j]]) low[x]=min(low[x],dfn[son[j]]);
	if (low[x]==dfn[x])
	 while (stk[stk[0]+1]!=x)
	  instk[stk[stk[0]]]=0,to[stk[stk[0]--]]=x;
}
bool check(int m){
	memset(lnk,0,sizeof(lnk));tot=0;
	for (int i=1;i<=m;i++)
	 add(p[a[i].x],a[i].y),add(p[a[i].y],a[i].x);
	memset(dfn,0,sizeof(dfn));
	memset(instk,0,sizeof(instk));stk[0]=tim=0;
	for (int i=1;i<=n;i++)
	 if (!dfn[i]) tarjan(i);
	for (int i=1;i<=n;i++)
	 if (to[i]==to[p[i]]) return 0;
	return 1;
}
int main(){
	for (N=red(),M=red();N;N=red(),M=red()){
		n=N*2;
		for (int i=1,x,y;i<=N;i++)
		 x=red()+1,y=red()+1,p[x]=y,p[y]=x;
		for (int i=1;i<=M;i++)
		 a[i].x=red()+1,a[i].y=red()+1;
		int L=1,R=M,ans;
		while (L<=R){
			int mid=L+R>>1;
			if (check(mid)) ans=mid,L=mid+1;else R=mid-1;
		}
		printf("%d\n",ans);
	}
	return 0;
}
```