[题面在这里](http://poj.org/problem?id=2420)

给定N个点，求这些点的广义费马点
模拟退火的经典题……

因为G++的原因WA了一下午……
万恶的POJ

初始值随便给个正常点的
我是用了横、纵坐标的平均值作为初始位置

每次随机一个角度，往这个方向走Temp步
这样作为下一状态

控制一下降温系数以及内循环次数就可以A啦

示例程序：

```
#include<cstdio>
#include<cmath>
#include<cstdlib>
#define sqr(x) ((x)*(x))
const int maxn=105;
const double eps=0.01,coe=0.99,PI=3.1415926535897932384626433832795;
int n;
struct point{
	double x,y;
	point() {}
	point(double a,double b):x(a),y(b) {}
}a[maxn],now(0,0);
double dis(point a,point b){
	return sqrt(sqr(a.x-b.x)+sqr(a.y-b.y));
}
double getans(point x){
	double res=0;
	for (int i=1;i<=n;i++) res+=dis(x,a[i]);
	return res;
}
bool check(double delta,double T){
	if (delta<=0) return 1;
	return rand()<=exp(-delta/T)*RAND_MAX;
}
int main(){
	srand(23333);
	scanf("%d",&n);
	for (int i=1;i<=n;i++)
	 scanf("%lf%lf",&a[i].x,&a[i].y),
	 now.x+=a[i].x,now.y+=a[i].y;
	now.x/=n;now.y/=n;
	for (double tem=100000;tem>eps;tem*=coe)
	 for (int i=1;i<=100;i++){
		double alpha=PI*2*rand()/RAND_MAX;
		point xx(now.x+cos(alpha)*tem,now.y+sin(alpha)*tem);
		if (check(getans(xx)-getans(now),tem)) now=xx;
	 }
	printf("%.0lf\n",getans(now));
	return 0;
}
```

