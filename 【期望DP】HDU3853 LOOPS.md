# 【期望DP】HDU3853 LOOPS

[题面在这里](http://acm.hdu.edu.cn/showproblem.php?pid=3853)



显然直接倒推就好了



示例程序：

```C++
#include<cstdio>

const int maxn=1005;
int n,m;
double f[maxn][maxn],p[maxn][maxn][3];
int main(){
	while (~scanf("%d%d",&n,&m)){
		for (int i=1;i<=n;i++)
		 for (int j=1;j<=m;j++)
		  scanf("%lf%lf%lf",&p[i][j][0],&p[i][j][1],&p[i][j][2]);
		f[n][m]=0;
		for (int i=n;i;i--)
		 for (int j=m;j;j--)
		  if (!(i==n&&j==m)&&p[i][j][0]!=1)
		   f[i][j]=(p[i][j][1]*f[i][j+1]+p[i][j][2]*f[i+1][j]+2)/(1-p[i][j][0]);
		printf("%.3lf\n",f[1][1]);
	}
	return 0;
}
```

