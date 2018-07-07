# 【分数规划，最优比率生成树】POJ 2728 Desert King

[题面在这里](http://poj.org/problem?id=2728)



分数规划经典题目啊……



考虑二分答案$mid$

则只需验证:
$$
\frac {\sum cost} {\sum dist}\le mid \\
\sum cost\le mid*\sum dist \\
\sum (cost_i-mid*dist_i)
$$
然后就直接最小生成树了



示例程序：

```c++
#include<cstdio>
#include<cmath>
#include<cstring>
#include<algorithm>
#define cl(x,y) memset(x,y,sizeof(x))
#define sqr(x) ((x)*(x))
#define _abs(x) ((x)>0?(x):-(x))
using namespace std;
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=(res<<3)+(res<<1)+ch-48,ch=nc();
	return res*f;
}

const int maxn=1005;
const double eps=1e-5;
int n,x[maxn],y[maxn],z[maxn],cst[maxn][maxn];
double dst[maxn][maxn],d[maxn];
bool vis[maxn];
inline double getdst(int x,int y,int xx,int yy){
	return sqrt(sqr(x-xx)+sqr(y-yy));
}
bool check(double mid){
	cl(vis,0);vis[1]=1;
	for (int i=1;i<=n;i++) d[i]=cst[1][i]-mid*dst[1][i];
	double res=0;
	for (int i=1;i<n;i++){
		int k;double Min=1e100;
		for (int j=1;j<=n;j++)
		 if (!vis[j]&&Min>d[j]) Min=d[j],k=j;
		vis[k]=1;res+=Min;
		for (int j=1;j<=n;j++)
		 if (!vis[j]) d[j]=min(d[j],cst[k][j]-mid*dst[k][j]);
	}
	return res+1e-10<=0;
}
int main(){
	n=red();
	while (n){
		for (int i=1;i<=n;i++)
		 x[i]=red(),y[i]=red(),z[i]=red();
		double L=0,R=0,ans;
		for (int i=1;i<=n;i++)
		 for (int j=1;j<=n;j++)
		  dst[i][j]=getdst(x[i],y[i],x[j],y[j]),
		  cst[i][j]=_abs(z[i]-z[j]),R=max(R,cst[i][j]/dst[i][j]);
		while (R-L>=eps){
			double mid=(L+R)/2;
			if (check(mid)) ans=mid,R=mid;else L=mid;
		}
		printf("%.3f\n",ans);
		n=red();
	}
	return 0;
} 
```

