# 【BFS+链表】BZOJ1098 [POI2007]办公楼biu

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1098)



显然是求补图的联通块个数……

考虑每次找一个点扩展，那么已经遍历过的点就可以无视了

因为每个点只能属于一个联通块。

用链表来实现点的删除，这样就可以只访问未删除的点了

由于每个点只被遍历一次，所以总复杂度为$O(n+m)$



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

const int maxn=100005,maxe=4000005;
int n,e,L[maxn],R[maxn],a[maxn],que[maxn];
int tot,son[maxe],lnk[maxn],nxt[maxe];
bool vis[maxn],usd[maxn];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
inline void del(int x){
	L[R[x]]=L[x];
	R[L[x]]=R[x];
}
int main(){
	n=red(),e=red();
	for (int i=1,x,y;i<=e;i++) x=red(),y=red(),add(x,y),add(y,x);
	for (int i=1;i<=n;i++) L[i]=i-1,R[i]=i+1;
	L[0]=0;R[0]=1;L[n+1]=n;R[n+1]=n+1;
	while (R[0]<=n){
		int hed=0,til=1;
		que[til]=R[0];vis[R[0]]=1;del(R[0]);
		while (hed!=til){
			int x=que[++hed];
			for (int j=lnk[x];j;j=nxt[j]) usd[son[j]]=1;
			for (int j=R[0];j<=n;j=R[j])
			 if (!usd[j]&&!vis[j])
			  que[++til]=j,vis[j]=1,del(j);
			for (int j=lnk[x];j;j=nxt[j]) usd[son[j]]=0;
		}
		a[++a[0]]=til;
	}
	sort(a+1,a+1+a[0]);
	printf("%d\n",a[0]);
	for (int i=1;i<=a[0];i++) printf("%d ",a[i]);
	return 0;
}
```

