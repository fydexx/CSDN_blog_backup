[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1576)

此题最重要的思想就是建立“最短路径树”。
何为最短路径树？就是最短路经过的所有点和边构成的一棵树（以前竟然没有发现这个东西）

考虑不经过最短路的最后一条边，其实就是把点x到父亲的边去掉
那么剩下的最短路径必定是这样的：
由一条不在树上的边(u,v)连接x子树和其余部分，则路径为1~u~v~x
可以发现，整个过程只走了一次不在树上的边，这样就保证了答案必定最优
这个方案的答案为：dst[u]+dst[v]+w(u,v)-dst[x]

然后就有两种解法：
【可并堆】
对于每棵子树，建立一个小根堆维护dst[u]+dst[v]+w(u,v)的最小值即可
从叶往根处理，每次合并各个儿子的堆，为了效率用可并堆（当然你也可以启发式合并一发）这里我用左偏树实现
就解决了，时间复杂度$O(NlogN)$

附上代码：

```
#include<cstdio>
#include<queue>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=100005,maxe=400005;
struct data{
	int id,w,fa;
	data (int x,int y,int z): id(x),w(y),fa(z) {}
	bool operator<(const data&b)const{
		return w>b.w;
	}
};
int n,e,ans[maxn],dst[maxn],fa[maxn];
struct edge{
	int tot,lnk[maxn],son[maxe],nxt[maxe],w[maxe];
	edge() {tot=0;}
	void add(int x,int y,int wi){
		son[++tot]=y;nxt[tot]=lnk[x];w[tot]=wi;lnk[x]=tot;
	}
}a,b;
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int getfa(int x){
	if (fa[x]==x) return x;
	return fa[x]=getfa(fa[x]);
}
priority_queue<data> Q;
bool vis[maxn];
void DIJ(){
	memset(dst,63,sizeof(dst));
	dst[1]=0;Q.push(data(1,0,1));
	while (!Q.empty()){
		while (!Q.empty()&&vis[Q.top().id]) Q.pop();
		if (Q.empty()) break;int k=Q.top().id,fa=Q.top().fa;Q.pop();
		if (k!=1) b.add(fa,k,dst[k]-dst[fa]);
		vis[k]=1;
		for (int j=a.lnk[k];j;j=a.nxt[j])
		 if (dst[a.son[j]]>dst[k]+a.w[j])
		  dst[a.son[j]]=dst[k]+a.w[j],
		  Q.push(data(a.son[j],dst[a.son[j]],k));
	}
}
struct node{
	int l,r,w,d,id;
	bool operator<(const node&b)const{
		return w<b.w;
	}
	node() {}
	node(int x,int i):w(x),id(i),d(0),l(0),r(0) {}
}tre[maxe];
int rot[maxn],len;
int merge(int a,int b){
	if (!a||!b) return a+b;
	if (tre[b]<tre[a]) swap(a,b);
	tre[a].r=merge(tre[a].r,b);
	if (tre[tre[a].l].d<tre[tre[a].r].d) swap(tre[a].l,tre[a].r);
	if (!tre[a].r) tre[a].d=0;else tre[a].d=tre[tre[a].r].d+1;
	return a;
}
int unite(int x,int y){
	int fx=getfa(x),fy=getfa(y);
	fa[fx]=fy;
}
void dfs(int x,int father){
	for (int j=b.lnk[x];j;j=b.nxt[j])
	 dfs(b.son[j],x),fa[x]=unite(x,b.son[j]);
	
	rot[x]=0;
	for (int j=a.lnk[x];j;j=a.nxt[j])
	 if (a.son[j]!=father&&getfa(a.son[j])!=getfa(x))
	  tre[++len]=node(dst[a.son[j]]+dst[x]+a.w[j],a.son[j]),rot[x]=merge(rot[x],len);

	for (int j=b.lnk[x];j;j=b.nxt[j])
	 rot[x]=merge(rot[x],rot[b.son[j]]);
	while (getfa(tre[rot[x]].id)==getfa(x)) rot[x]=merge(tre[rot[x]].l,tre[rot[x]].r);
	ans[x]=tre[rot[x]].w-dst[x];
}
int main(){
	n=red(),e=red();
	for (int i=1;i<=n;i++) fa[i]=i;
	for (int i=1,x,y,w;i<=e;i++) x=red(),y=red(),w=red(),a.add(x,y,w),a.add(y,x,w);
	DIJ();len=0;
	dfs(1,1);
	for (int i=2;i<=n;i++)
	 if (ans[i]<0) printf("-1\n");else printf("%d\n",ans[i]);
	return 0;
}
```

【树链剖分】

想法与上面类似
我们枚举每条边(u,v)，假设u在x子树外，v在x子树内
则可以用dst[u]+dst[v]+w(u,v)来更新x~v路径上所有点的最小值
这样就可以用树链剖分+线段树来维护这个最小值了
最后输出答案时再-dst[i]

想法相似，所以没有配代码……