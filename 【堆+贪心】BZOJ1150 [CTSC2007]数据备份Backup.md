# 【堆+贪心】BZOJ1150 [CTSC2007]数据备份Backup

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1150)



先把整个数组差分一下，问题就转化成了：

从n个元素中选择各不相邻的k个，使得其加和最小

用堆来维护。

每次中堆中取出权值最小的一个元素$x$，计入答案

然后将其与相邻的两个元素$l,r$合并为一个新的元素，权值为$l+r-x$

将新元素放入堆中

如此k次即可。



可以用双向链表维护元素的相邻关系。



此方法的正确性在于：

当从堆顶取得一个新元素时，就相当于撤销了$x$，改为取$l$和$r$

这样取的次数仍然等于取的元素个数。



注意边界情况。

示例程序：

```C++
#include<cstdio>
#include<queue>
using namespace std;
typedef long long LL;

const int maxn=100005,INF=0x3f3f3f3f;
int n,k,a[maxn],L[maxn],R[maxn];
bool vis[maxn];
LL ans;
struct data{
	int id,x;
	data () {}
	data (int _id,int _x):id(_id),x(_x) {}
	bool operator<(const data&b)const{
		return x>b.x;
	}
};
priority_queue<data> Q;
int main(){
	int lst;scanf("%d%d%d",&n,&k,&lst);
	for (int i=1;i<n;i++){
		int x;scanf("%d",&x);L[i]=i-1;R[i]=i+1;
		a[i]=x-lst;lst=x;Q.push(data(i,a[i]));
	}
	n--;a[0]=INF;a[n+1]=INF;
	R[0]=1;L[n+1]=n;L[0]=0;R[n+1]=n+1;
	for (int i=1;i<=k;i++){
		while (Q.top().id!=R[L[Q.top().id]]||Q.top().id!=L[R[Q.top().id]]) Q.pop();
		data x=Q.top();Q.pop();
		ans+=x.x;
		a[x.id]=a[L[x.id]]+a[R[x.id]]-x.x;
		Q.push(data(x.id,a[x.id]));
		R[L[x.id]=L[L[x.id]]]=x.id;
		L[R[x.id]=R[R[x.id]]]=x.id;
	}
	printf("%lld",ans);
	return 0;
}
```

