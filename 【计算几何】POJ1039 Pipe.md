[题面在这里](http://poj.org/problem?id=1039)

这是我A的第一道真正意义上的计算几何题……
一开始想法不好，重写了两次，花了两个半天才A掉……各位见笑了


题意大致如下：给定一根由上下两条折线构成的管道（上下壁均不透明、不反光），求光线是否能穿过管道，或输出最远能到的距离。

那么有这样一个推论：最优的线路一定经过上端点、下端点至少各一个。因为若光线不经过任何端点，就完全可以旋转、平移它，使其射得更远，那么经过端点就是移动的极端情况，就有可能最优。
于是可以直接枚举上下两个端点，引一条直线，依次判断是否穿过每个弯的间隔（我们把x坐标相同的一对端点之间的线段称为间隔）若穿过所有间隔就输出，否则还要更新最远距离

贴个代码：

```
#include<cstdio>
#include<cmath>
#include<algorithm>
using namespace std;
const int maxn=25;
const double eps=1e-4;
int n;
struct point{
	double x,y;
	point(double x=0,double y=0):x(x),y(y) {}
}a[maxn],b[maxn];
struct line{
	double k,b;
	void get(point x,point y){
		k=(x.y-y.y)/(x.x-y.x);
		b=x.y-k*x.x;
	}
	double gety(double x){return k*x+b;}
}l[45];
int dcmp(double a,double b){
	if (fabs(a-b)<eps) return 0;
	if (a<b) return -1;
	return  1;
}
double intersect(line a,line b){
	return (b.b-a.b)/(a.k-b.k);
}
int main(){
	scanf("%d",&n);
	while (n){
		for (int i=1;i<=n;i++){
			double x,y;scanf("%lf%lf",&x,&y);
			a[i]=point(x,y);b[i]=point(x,y-1);
			if (i>1){
				l[i*2-1].get(a[i],a[i-1]);
				l[i*2].get(b[i],b[i-1]);
			}
		}
		double ans=-(1e100);bool flg=0;
		for (int i=1;i<=n&&!flg;i++)
		 for (int j=1;j<=n&&!flg;j++)
		  if (i!=j){
		  	line now;now.get(a[i],b[j]);int k;
		  	for (k=1;k<=n;k++){
		  		double y=now.gety(a[k].x);
		  		if (!(dcmp(b[k].y,y)<=0&&dcmp(y,a[k].y)<=0)) break;
			}
			if (k>n) {flg=1;break;}
			if (k<=max(i,j)) continue;
			ans=max(ans,max(intersect(now,l[k*2-1]),intersect(now,l[k*2])));
		  }
		if (flg) printf("Through all the pipe.\n");else printf("%.2f\n",ans);
		scanf("%d",&n);
	}
	return 0;
}
```

