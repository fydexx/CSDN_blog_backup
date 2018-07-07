# 【边双联通分量】hihoCoder#1184 连通性二·边的双连通分量

[题面在这里](http://hihocoder.com/problemset/problem/1184)



其实就是边双的裸题



示例程序：

```C++
//hihocoder 1184
#include<cstdio>
#include<cstring>
#include<algorithm>
#define cl(x,y) memset(x,y,sizeof(x))
using namespace std;
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=(res<<3)+(res<<1)+ch-48,ch=nc();
	return res*f;
}

const int maxn=20005,maxe=200005;
int n,e;
int tot,son[maxe],lnk[maxn],nxt[maxe];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
int times,in[maxn],low[maxn];
bool isb[maxe];
void tarjan(int x,int fa){
	low[x]=in[x]=++times;
	for (int j=lnk[x];j;j=nxt[j])
	 if (!in[son[j]]){
	 	tarjan(son[j],x);
	 	low[x]=min(low[x],low[son[j]]);
	 	if (low[son[j]]>dfn[x]) isb[j]=isb[j^1]=1;
	 }else if (son[j]!=fa) low[x]=min(low[x],in[son[j]]);
}
int BCC,bcc[maxn],Min[maxn];
void bl(int x){
	bcc[x]=BCC;Min[BCC]=min(Min[BCC],x);
	for (int j=lnk[x];j;j=nxt[j])
	 if (!isb[j]&&!bcc[son[j]]) bl(son[j]);
}
int main(){
	n=red(),e=red();tot=1;
	for (int i=1,x,y;i<=e;i++) x=red(),y=red(),add(x,y),add(y,x);
	for (int i=1;i<=n;i++)
	 if (!in[i]) tarjan(i,0);
	cl(Min,63);
	for (int i=1;i<=n;i++)
	 if (!bcc[i]) BCC++,bl(i);
	printf("%d\n",BCC);
	for (int i=1;i<=n;i++) printf("%d ",Min[bcc[i]]);
	return 0;
} 
```

