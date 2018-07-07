【前言】
----

A*是被广泛运用于实际生活的一种搜索算法，在OI中也非常实用。

【A*的原理】
-------

我们来回想一下BFS的原理：
每次从队首取一个状态，用其扩展出新的状态并放入队尾。
那么这个队列是没有优先级的

A*其实与BFS类似，只是给每个状态定义了估价函数f(x)
用于估计此状态最终到达目标状态的总代价。
只需要让代价小的先扩展，就能尽可能地将复杂度降到最低。

此时估价函数f(x)的选取就很重要了
因为估价越准确，就越容易使代价最小的先到达目标状态。
f(x)包括两个方面，即f(x)=h(x)+g(x)
其中h(x)是起始状态到当前状态的代价，g(x)是当前状态到目标状态的估计代价。
g(x)根据题目需要选取。

【A*的应用】
-------

A*最常见的应用就是求第K短路。
直接把每个点x到终点T的最短路作为g(x)
此时的估价函数称为“完美估价函数”，因为它的估计值完全准确。
这样，第K短路一定是第K个到达终点的。
g(x)可以先用单源最短路算法预处理出来。

例题：POJ 2449
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

