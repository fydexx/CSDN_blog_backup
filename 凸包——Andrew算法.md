什么是凸包？
------
凸包的定义如下：
在一个点集D中，按一定顺序选取子集Q
使得Q中所有点顺次连接所构成的封闭凸多边形包住D中所有点

可以形象地理解为：有许多个钉子钉在平面上，用一根牛皮筋把所有点包住
如下图：
![这里写图片描述](http://img.blog.csdn.net/20170604203158466?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

常用算法
---------
凸包问题的一般解法有：Graham算法、Melkman算法、Andrew算法等
由于Andrew算法代码简便，效率比较高，笔者更推荐使用Andrew算法

Andrew算法是Graham算法的变种。
其主要思想为把凸包上的点依次放入栈中，
如果发现形成了凹多边形（叉积为负值）
就删除一些点，使得又能够维持凸的形态。

这时就会发现，处理各个点需要按照x从左往右的顺序，排序即可
当然，这只是处理了下凸的一个凸壳，倒过来再刷一次，就得到了整个凸包

例题：[HDU1392](http://acm.hdu.edu.cn/showproblem.php?pid=1392)
为了使代码更有通用性，这里使用double进行运算

```
#include<cstdio>
#include<cmath>
#include<algorithm>
using namespace std;
const double eps=1e-7;
const int maxn=105;
int n;
struct point{
	double x,y;
	point() {}
	point(double a,double b):x(a),y(b) {}
	bool operator<(const point&b)const{
		if (x<b.x) return 1;
		if (x>b.x) return 0;
		return y<b.y;
	}
	point operator-(const point&b) {return point(x-b.x,y-b.y);}
}a[maxn],stk[maxn];
typedef point vec;
int dcmp(double x){
	if (fabs(x)<=eps) return 0;
	return x>0?1:-1;
}
double getdst(point a,point b){
	return sqrt((a.x-b.x)*(a.x-b.x)+(a.y-b.y)*(a.y-b.y));
}
double cross(vec a,vec b){
	return a.x*b.y-a.y*b.x;
}
int Andrew(){
	sort(a+1,a+1+n);
	int len=0;
	for (int i=1;i<=n;i++){
		while (len>1&&dcmp(cross(stk[len]-stk[len-1],a[i]-stk[len-1]))==-1) len--;
		stk[++len]=a[i];
	}
	int k=len;
	for (int i=n-1;i>=1;i--){
		while (len>k&&dcmp(cross(stk[len]-stk[len-1],a[i]-stk[len-1]))==-1) len--;
		stk[++len]=a[i];
	}
	return len;
}
int main(){
	for (scanf("%d",&n);n;scanf("%d",&n)){
		for (int i=1;i<=n;i++) scanf("%lf%lf",&a[i].x,&a[i].y);
		int t=Andrew();
		double ans=0;
		for (int i=1;i<t;i++) ans+=getdst(stk[i],stk[i+1]);
		printf("%.2lf\n",n==2?ans/2:ans);
	}
	return 0;
}
```