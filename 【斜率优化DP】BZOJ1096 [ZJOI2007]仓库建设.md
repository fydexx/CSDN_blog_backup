[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1096)

典型的斜率优化DP……

定义$f[i]$表示前i个工厂已经处理（在第i个工厂修建仓库）的最小费用
剩下的事情，就是去枚举离i最近的仓库是哪个
$$f[i]=Min\{f[j]+c[i]+d[i]*(g[i]-g[j])-(s[i]-s[j])\}$$
其中g[]是p[]的前缀和，s[]是d[]*p[]的前缀和
用于统计j+1~i这一段所有工厂的运输费用
然后：
$$f[i]=Min\{f[j]-d[i]g[j]+s[j]\}+c[i]+d[i]g[i]-s[i]$$
设：
$k=d[i]$
$x=g[j]$
$y=f[j]+s[j]$
$b=Min\{\}里面的东西$
则有：
$b=-kx+y$
$y=kx+b$

直接套用斜率优化即可

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define LL long long
using namespace std;
const int maxn=1000005;
#define nc getchar
inline int red(){
	int tot=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=nc();
	return tot*f;
}
int n;
LL s[maxn],g[maxn],d[maxn],f[maxn],c[maxn];
struct point{
	LL x,y;
	point() {}
	point(LL a,LL b):x(a),y(b) {}
	point operator - (const point&b) {return point(x-b.x,y-b.y);}
}stk[maxn];
typedef point vec;
LL cross(vec a,vec b){
	return a.x*b.y-a.y*b.x;
}
LL getb(point a,LL k){
	return -k*a.x+a.y;
}
int main(){
	n=red();
	for (int i=1,pi;i<=n;i++)
	 d[i]=red(),pi=red(),c[i]=red(),g[i]=g[i-1]+pi,s[i]=s[i-1]+d[i]*pi;
	memset(f,63,sizeof(f));
	f[0]=0;
	int len=1,now=1;stk[1]=point(0,0);
	for (int i=1;i<=n;i++){
		LL k=d[i];
		while (now<len&&getb(stk[now+1],k)<getb(stk[now],k)) now++;
		f[i]=getb(stk[now],k)+c[i]+d[i]*g[i]-s[i];
		point a(g[i],f[i]+s[i]);
		while (len>1&&cross(stk[len]-stk[len-1],a-stk[len-1])<0) len--;
		now=min(now,len);stk[++len]=a;
	}
	printf("%lld",f[n]);
	return 0;
}
```