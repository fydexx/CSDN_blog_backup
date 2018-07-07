# 【状压DP】UOJ#265 【NOIP2016】愤怒的小鸟

[题面在这里](http://uoj.ac/problem/265)



其实就是一个简单的状压DP

如果你直接$O(2^n\cdot n^3)$暴搞肯定是不行的

那么就预处理一下$g_{i,j}$表示经过i,j两头猪的抛物线经过的猪

然后……就好了



示例程序：

```C++
#include<cstdio>
#include<cstring>
#include<algorithm>
#define _abs(x) ((x)>0?(x):-(x))
#define cl(x,y) memset(x,y,sizeof(x))
using namespace std;

const int maxn=25,maxs=270000,INF=0x3f3f3f3f;
const double eps=1e-10;
int tst,n,g[maxn][maxn],f[maxs],a[maxn];
int dcmp(double x){
	if (_abs(x)<=eps) return 0;
	return x>0?1:-1;
}
struct point{
	double x,y;
}p[maxn];
int main(){
	scanf("%d",&tst);
	while (tst--){int d;
		scanf("%d%d",&n,&d);
		for (int i=0;i<n;i++) scanf("%lf%lf",&p[i].x,&p[i].y);
		cl(g,0);cl(f,63);
		for (int i=0;i<n;i++)
		 for (int j=0;j<n;j++)
		 if (i!=j&&dcmp(p[i].x-p[j].x)!=0){
			double a=(p[i].y*p[j].x-p[j].y*p[i].x)/((p[i].x-p[j].x)*p[i].x*p[j].x),b=(p[i].y-a*p[i].x*p[i].x)/p[i].x;
			if (dcmp(a)>=0) continue;
			int s=0;
			for (int k=0;k<n;k++)
			 if (dcmp(p[k].y-(p[k].x*p[k].x*a+p[k].x*b))==0) s|=(1<<k);
			g[i][j]=s;
		 }
		f[0]=0;
		for (int s=0;s<(1<<n);s++){
			a[0]=0;
			for (int i=0;i<n;i++)
			 if ((s&(1<<i))==0) a[++a[0]]=i;
			for (int i=1;i<=a[0];i++) f[s|(1<<a[i])]=min(f[s|(1<<a[i])],f[s]+1);
			for (int i=1;i<=a[0];i++)
			 for (int j=i+1;j<=a[0];j++)
			  if (g[a[i]][a[j]]){
			  	f[s|g[a[i]][a[j]]]=min(f[s|g[a[i]][a[j]]],f[s]+1);
			  }
		}
		printf("%d\n",f[(1<<n)-1]);
	}
	return 0;
}
```

