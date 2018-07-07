前言
--
最近才入了DP优化的大坑……
发现斜率优化DP还是很有用的
下面会结合一道例题，同时讲解斜率优化DP

正文
--
先看一下例题：[HDU3507](http://acm.hdu.edu.cn/showproblem.php?pid=3507)
题目大意：有一串数列，要把它分割成若干段
每一段的代价是 这一段所有数的和 的平方 再加上常数M
求最小的代价和。

DP很好推啊，$f[i]$表示前i个数字的最小代价和
$$f[i]=Min\{f[j]+(s[i]-s[j])^2\}+M$$
其中s[]是原数列的前缀和
按如下方式转化：
$$f[i]=Min\{f[j]+s[i]^2-2s[i]s[j]+s[j]^2\}+M$$
$$f[i]=Min\{f[j]-2s[i]s[j]+s[j]^2\}+M+s[i]^2$$
设$k=2s[i]，x=s[j]，y=f[j]+s[j]^2$
由于$Min\{\}$外面的部分只与$i$有关，可以先不考虑
设里面的部分为$b$，则有：
$b=-kx+y$
$y=kx+b$

你会发现，这不就是一条直线嘛
我们来好好思考一下这条直线与DP之间的联系：
前面已求得的每个状态$j$都被抽象成二维平面上的一个点$(x,y)$
$b$是我们要求的最小值，而斜率$k$只与$i$有关
也就是说，要过所有的点做一条斜率为$k$的直线，
所有直线截距的最小值就是我们的$b$
如下图：
![这里写图片描述](http://img.blog.csdn.net/20170605195602093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

不难发现，能取到最小截距的状态$j(x,y)$一定在当前的 下凸壳 上
再加上s[j]随j递增（即x坐标递增）
我们就可以用凸包算法Andrew动态维护一个下凸壳
本题又有一个很好的性质，就是斜率$k$也是递增的
这导致了每次选取的状态j也是递增的
那么就可以用一个指针记录上次选取的状态，每次往后找即可
时间复杂度$O(n)$

扩展讨论
--
上述问题中，如果斜率$k$不递增，那就只能在凸壳上二分了：
通过判断 mid左右两侧直线的斜率 与k的大小关系 来缩小范围
时间复杂度$O(nlogn)$

如果题目再恶心一点，连新加入点的x都不递增呢？
那就需要写一个Splay维护凸壳，或者CDQ分治
时间复杂度$O(nlogn)$

示例程序
----

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define LL long long
using namespace std;
const int maxn=500005;
int n,m;
LL s[maxn],f[maxn];
#define nc getchar
inline int red(){
	int tot=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=nc();
	return tot*f;
}
struct point{
	LL x,y;
	point() {}
	point(LL a,LL b):x(a),y(b) {}
	point operator-(const point&b) {return point(x-b.x,y-b.y);}
}stk[maxn];
typedef point vec;
LL cross(vec a,vec b){
	return a.x*b.y-b.x*a.y;
}
LL getb(point a,LL k){
	return -a.x*k+a.y;
}
int main(){
	while (scanf("%d%d",&n,&m)==2){
		for (int i=1;i<=n;i++) s[i]=s[i-1]+red();
		memset(f,63,sizeof(f));
		f[0]=0;
		int len=1,now=1;stk[1]=point(0,0);
		for (int i=1;i<=n;i++){
			LL k=2*s[i];
			while (now<len&&getb(stk[now+1],k)<getb(stk[now],k)) now++;
			f[i]=getb(stk[now],k)+m+s[i]*s[i];
			point a(s[i],f[i]+s[i]*s[i]);
			while (len>1&&cross(stk[len]-stk[len-1],a-stk[len-1])<=0){
				len--;if (now>len) now--;
			}
			stk[++len]=a;
		}
		printf("%lld\n",f[n]);
	}
	return 0;
}
```

总结
--
斜率优化DP的存在，再一次提醒我们数形结合的重要性
在OI中，类似的例子还有很多……
但是，在运用数形结合思想时，要注意“形”与“数”之间的联系
切勿将想法匆匆过一遍就用代码实现，容易搞混
