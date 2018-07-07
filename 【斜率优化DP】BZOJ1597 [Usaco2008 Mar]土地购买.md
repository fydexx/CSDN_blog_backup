# 【斜率优化DP】BZOJ1597 [Usaco2008 Mar]土地购买

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1597)



一看就是斜率优化DP……



首先如果存在$a_i\le a_j且b_i\le b_j$则可以不处理i

去掉这些没用的土地，那么$a_i$一定递增，$b_i$一定递减

设$f_i$表示前$i$块土地全部买了的最小代价

很容易得到转移方程：

$$f_i=Min\{ f_j+a_i\times b_{j+1} \}\space\space\space\space(0\le j\lt i)$$

然后$k=a_i,x=-b_{j+1},y=f_j$

就好了

示例程序：

```C++
#include<cstdio>
#include<algorithm>
#define LL long long
using namespace std;
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res*f;
}

const int maxn=50005;
int n,N,m[maxn],now,len;
LL f[maxn];
struct data{
	int x,y;
	bool operator<(const data&b)const{
		return x<b.x||x==b.x&&y<b.y;
	}
}a[maxn];
struct point{
	LL x,y;
	point() {}
	point (LL _x,LL _y):x(_x),y(_y) {}
	point operator-(const point&b){
		return point(x-b.x,y-b.y);
	}
}stk[maxn];
typedef point vec;
LL det(vec a,vec b){
	return a.x*b.y-a.y*b.x;
}
LL getb(point p,LL k){
	return p.y-p.x*k;
}
void add(point x){
	while (len>1&&det(stk[len]-stk[len-1],x-stk[len-1])<=0) len--;
	now=min(now,len);stk[++len]=x;
}
point get(LL k){
	while (now<len&&getb(stk[now+1],k)<getb(stk[now],k)) now++;
	return stk[now];
}
int main(){
	n=red();
	for (int i=1;i<=n;i++) a[i].x=red(),a[i].y=red();
	sort(a+1,a+1+n);
	for (int i=n;i>=1;i--) m[i]=max(m[i+1],a[i].y);
	for (int i=1;i<=n;i++)
	 if (a[i].y>m[i+1]) a[++N]=a[i];
	f[0]=0;
	now=len=1;stk[1]=point(-a[1].y,0);
	for (int i=1;i<=N;i++){
	 	LL k=a[i].x;
	 	point p=get(k);
	 	f[i]=getb(p,k);
	 	add(point(-a[i+1].y,f[i]));
	 }
	printf("%lld",f[N]);
	return 0;
}
```

