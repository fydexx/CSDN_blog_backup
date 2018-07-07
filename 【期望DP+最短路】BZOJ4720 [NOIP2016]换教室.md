[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=4720)

一道简单的期望DP题……

定义$f[i][j][0/1]$表示：
前i节课，已经用了j次申请机会，第i节课申请/不申请
那么显然，根据期望的线性性，可以得到：

$$f[i][j][0]=Min(f[i-1][j][0]+w(c[i-1],c[i]),f[i-1][j][1]+k[i-1]*w(d[i-1],c[i])+(1-k[i-1])*w(c[i-1],c[i]))$$
$$f[i][j][1]=Min(f[i-1][j-1][0]+k[i]*w(c[i-1],d[i])+(1-k[i])*w(c[i-1],c[i]),f[i-1][j-1][1]+ k[i]*k[i-1]*w(d[i-1],d[i]) + k[i]*(1-k[i-1])*w(c[i-1],d[i]) + (1-k[i])*k[i-1]*w(c[i],d[i-1]) + (1-k[i])*(1-k[i-1])*w(c[i],c[i-1]))$$

其中w(i,j)表示点i到点j的最短路径
那么用Floyd预处理出w(i,j)即可

附上代码：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define w(x,y) g[x][y]
using namespace std;
const int maxn=2005,maxv=305;
int n,m,v,e,g[maxv][maxv],c[maxn],d[maxn];
double k[maxn],f[maxn][maxn][2];
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
void floyd(){
	for (int k=1;k<=v;k++)
	 for (int i=1;i<=v;i++)
	  for (int j=1;j<=v;j++)
	   g[i][j]=min(g[i][j],g[i][k]+g[k][j]);
}
int main(){
	n=red(),m=red(),v=red(),e=red();
	for (int i=1;i<=n;i++) c[i]=red();
	for (int i=1;i<=n;i++) d[i]=red();
	for (int i=1;i<=n;i++) scanf("%lf",&k[i]);
	memset(g,63,sizeof(g));
	for (int i=1;i<=v;i++) g[i][i]=0;
	for (int i=1,x,y;i<=e;i++) x=red(),y=red(),g[x][y]=min(g[x][y],red()),g[y][x]=g[x][y];
	floyd();
	memset(f,127,sizeof(f));
	f[1][0][0]=f[1][1][1]=0;
	for (int i=2;i<=n;i++)
	 for (int j=0,tj=min(i,m);j<=tj;j++){
	 	f[i][j][0]=f[i-1][j][0]+w(c[i-1],c[i]);
	 	f[i][j][0]=min(f[i][j][0],f[i-1][j][1]+k[i-1]*w(d[i-1],c[i])+(1-k[i-1])*w(c[i-1],c[i]));
	 	if (!j) continue;
	 	f[i][j][1]=f[i-1][j-1][0]+k[i]*w(c[i-1],d[i])+(1-k[i])*w(c[i-1],c[i]);
	 	f[i][j][1]=min(f[i][j][1],f[i-1][j-1][1]+ k[i]*k[i-1]*w(d[i-1],d[i]) + k[i]*(1-k[i-1])*w(c[i-1],d[i]) + (1-k[i])*k[i-1]*w(c[i],d[i-1]) + (1-k[i])*(1-k[i-1])*w(c[i],c[i-1]));
	 }
	double ans=1e100;
	for (int j=0;j<=m;j++) ans=min(ans,min(f[n][j][0],f[n][j][1]));
	printf("%.2lf",ans);
	return 0;
}
```