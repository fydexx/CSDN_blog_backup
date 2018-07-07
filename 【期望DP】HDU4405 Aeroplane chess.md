# 【期望DP】HDU4405 Aeroplane chess

[题面在这里](http://acm.hdu.edu.cn/showproblem.php?pid=4405)



还是期望DP的老套路……

从后往前DP，$f_i$表示i到终点的期望值，然后XJB转移就好了

还有题意貌似是能飞就必须飞？没讲清楚啊



示例程序：

```C++
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;

const int maxn=100010;
int n,m,to[maxn];
double f[maxn];
int main(){
	scanf("%d%d",&n,&m);
	while (n){
		for (int i=0;i<=n;i++) to[i]=i;
		for (int i=1,x,y;i<=m;i++) scanf("%d%d",&x,&y),to[x]=y;
		memset(f,0,sizeof(f));
		for (int i=n-1;i>=0;i--){
			if (to[i]>i) {f[i]=f[to[i]];continue;}
			f[i]=1;
			for (int j=1;j<=6;j++)
			 f[i]+=f[i+j]/6;
		}
		printf("%.4lf\n",f[0]);
		scanf("%d%d",&n,&m);
	}
	return 0;
}
```

