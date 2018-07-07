[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1001)

对偶图应用的经典题……

初看题面，可以明显发现这是一道最小割的裸题
但是数据太大，直接T掉（貌似Dinic可以过）

仔细观察可以发现，这是一个S-T完全图
那么就可以转化为对偶图的最短路问题
关于最短路求最大流，可以参考[周冬《浅析最大最小定理在信息学竞赛中的应用》](http://download.csdn.net/detail/linkfqy/9855769)

下面讲一点自己的想法：
对偶图，指的是对于原图G，新建图G'
其中G'的所有点对应G中的所有面，G'中的边对应G中两个面的公共边
（G整个图的两侧对应G'的S和T）
这样，G'中的每条S~T路径都是G中的一个割
要求最小割其实就是S~T的最短路
SPFA水过~

附上代码：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=1005,maxs=2000005,maxe=6000005;
int n,m,S,T;
int tot,son[maxe],nxt[maxe],lnk[maxs],w[maxe];
void add(int x,int y,int z){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;w[tot]=z;
}
#define nc getchar
inline int red(){
	int tot=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=nc();
	return tot*f;
}
inline int getid(int x,int y,int d){
	if (x<1||y==m) return S;
	if (y<1||x==n) return T;
	return ((x-1)*m+y)*2+d;
}
int que[maxs],dst[maxs];
bool vis[maxs];
int spfa(){
	memset(dst,63,sizeof(dst));
	memset(vis,0,sizeof(vis));
	int hed=0,til=1;
	que[1]=S;dst[S]=0;
	while (hed!=til){
		int x=que[hed=(hed+1)%maxs];
		vis[x]=0;
		for (int j=lnk[x];j;j=nxt[j])
		 if (dst[son[j]]>dst[x]+w[j]){
		 	dst[son[j]]=dst[x]+w[j];
		 	if (!vis[son[j]])
		 	 vis[son[j]]=1,
		 	 que[til=(til+1)%maxs]=son[j];
		 }
	}
	return dst[T];
}
int main(){
	n=red(),m=red();S=0,T=1;
	for (int i=1;i<=n;i++)
	 for (int j=1;j<m;j++){
	 	int x=getid(i-1,j,1),y=getid(i,j,0),z=red();
	 	add(x,y,z);add(y,x,z);
	 }
	for (int i=1;i<n;i++)
	 for (int j=1;j<=m;j++){
	 	int x=getid(i,j-1,0),y=getid(i,j,1),z=red();
	 	add(x,y,z);add(y,x,z);
	 }
	for (int i=1;i<n;i++)
	 for (int j=1;j<m;j++){
	 	int x=getid(i,j,0),y=getid(i,j,1),z=red();
	 	add(x,y,z);add(y,x,z);
	 }
	printf("%d",spfa());
	return 0;
}
```