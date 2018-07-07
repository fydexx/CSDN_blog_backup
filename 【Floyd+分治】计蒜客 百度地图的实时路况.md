[题面在这里](https://nanti.jisuanke.com/t/11217)

此题思路很巧妙……好题，好题啊

首先要讲一下Floyd的本质其实就是一个DP
其中中介点k是DP的阶段，表示已经取了$[1.k]$的点作为中介点
当然，也可以改变枚举k的顺序，以获得想要的阶段

考虑分治算法。
对于区间$[l,r]$，分成$[l,mid]$和$[mid+1,r]$两段
利用Floyd算法，将$[mid+1,r]$作为中介点加入
然后往$[l,mid]$分治下去
另一半同理。
这样，当l==r时，只有1个点没有作为中介点，累计答案即可

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define LL long long
using namespace std;

const int maxn=305,INF=0x3f3f3f3f;
int n,f[maxn][maxn];
LL sol(int l,int r){
	LL res=0;
	if (l==r){
		for (int i=1;i<=n;i++)
		 for (int j=1;j<=n;j++)
		  if (i!=l&&j!=l) res+=f[i][j]==INF?-1:f[i][j];
		return res;
	}
	int tem[maxn][maxn],mid=l+r>>1;
	memcpy(tem,f,sizeof(f));
	for (int k=mid+1;k<=r;k++)
	 for (int i=1;i<=n;i++)
	  for (int j=1;j<=n;j++)
	   if (i!=j&&j!=k&&i!=k)
	    f[i][j]=min(f[i][j],f[i][k]+f[k][j]);
	res+=sol(l,mid);
	memcpy(f,tem,sizeof(tem));
	for (int k=l;k<=mid;k++)
	 for (int i=1;i<=n;i++)
	  for (int j=1;j<=n;j++)
	   if (i!=j&&j!=k&&i!=k)
	    f[i][j]=min(f[i][j],f[i][k]+f[k][j]);
	res+=sol(mid+1,r);
	memcpy(f,tem,sizeof(tem));
	return res;
}
int main(){
	scanf("%d",&n);
	for (int i=1;i<=n;i++)
	 for (int j=1;j<=n;j++){
	 	scanf("%d",&f[i][j]);
	 	if (f[i][j]<0) f[i][j]=INF;
	 }
	printf("%lld",sol(1,n));
	return 0;
}
```