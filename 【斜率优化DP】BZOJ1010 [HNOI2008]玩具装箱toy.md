[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1010)

一道典型的斜率优化DP……

很明显，定义$f[i]$表示前i个玩具的费用
可以得到：
$$f[i]=Min\{f[j]+(i-j-1+s[i]-s[j]-L)^2\}$$
其中$s[]$是$c[]$的前缀和，方便统计加和
如下变换：
$$f[i]=Min\{f[j]+((s[i]+i-L-1)+(-s[j]-j))^2\}$$
$$f[i]=Min\{f[j]+(s[j]+j)^2-2(s[i]+i-L-1)(s[j]+j)\}+(s[i]+i-L-1)^2$$
设：
$k=2(s[i]+i-L-1)$
$x=s[j]+j$
$y=f[j]+(s[j]+j)^2$
把Min{}里面的部分看做b
就有：
$b=-kx+y$
$y=kx+b$
然后就可以直接套用斜率优化DP了

附上代码：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define LL long long
#define sqr(x) ((x)*(x))
using namespace std;
const int maxn=50005;
int n,L;
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
	return -k*a.x+a.y;
}
int main(){
	n=red(),L=red();
	for (int i=1;i<=n;i++) s[i]=s[i-1]+red();
	memset(f,63,sizeof(f));
	f[0]=0;
	int len=1,now=1;stk[1]=point(0,0);
	for (int i=1;i<=n;i++){
		LL k=2*(i+s[i]-1-L);
		while (now<len&&getb(stk[now+1],k)<getb(stk[now],k)) now++;
		f[i]=getb(stk[now],k)+sqr(i+s[i]-1-L);
		point a(i+s[i],f[i]+sqr(i+s[i]));
		while (len>1&&cross(stk[len]-stk[len-1],a-stk[len-1])<0) len--;
		now=min(now,len);stk[++len]=a;
	}
	printf("%lld",f[n]);
	return 0;
}
```