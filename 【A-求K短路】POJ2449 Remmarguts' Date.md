[题面在这里](http://poj.org/problem?id=2449)

K短路裸题……
关于A*，[看这里](http://blog.csdn.net/linkfqy/article/details/72762954)

只要注意S==T的时候最短路不是0即可

附上代码：

```
#include<cstdio>
#include<queue>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=1005,maxe=100005;
int n,e,S,T,K,ans=-1;
struct edge{
	int son[maxe],nxt[maxe],lnk[maxn],w[maxe],tot;
	void add(int x,int y,int z){
		son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;w[tot]=z;
	}
}a,b;
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int que[maxn],dst[maxn];
bool vis[maxn];
void spfa(){
	memset(dst,63,sizeof(dst));
	memset(vis,0,sizeof(vis));
	int hed=0,til=1;
	que[1]=T;dst[T]=0;
	while (hed!=til){
		int x=que[hed=(hed+1)%maxn];
		vis[x]=0;
		for (int j=b.lnk[x];j;j=b.nxt[j])
		 if (dst[b.son[j]]>dst[x]+b.w[j]){
		 	dst[b.son[j]]=dst[x]+b.w[j];
		 	if (!vis[b.son[j]])
		 	 que[til=(til+1)%maxn]=b.son[j],
		 	 vis[b.son[j]]=1;
		 }
	}
}
struct data{
	int id,w;
	data(int x,int y):id(x),w(y) {}
	bool operator<(const data&b)const{
		return w+dst[id]>b.w+dst[b.id];
	}
};
priority_queue<data> Q;
void astar(){
	Q.push(data(S,0));
	while (!Q.empty()){
		data k=Q.top();Q.pop();
		if (k.id==T&&!(--K)) {ans=k.w;return;}
		for (int j=a.lnk[k.id];j;j=a.nxt[j])
		 Q.push(data(a.son[j],k.w+a.w[j]));
	}
}
int main(){
	n=red(),e=red();
	for (int i=1,x,y,z;i<=e;i++) x=red(),y=red(),z=red(),a.add(x,y,z),b.add(y,x,z);
	S=red(),T=red(),K=red();if (S==T) K++;
	spfa();
	astar();
	printf("%d",ans);
	return 0;
}
```