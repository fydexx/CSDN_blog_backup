# 【斜率优化DP】BZOJ4518 [Sdoi2016]征途

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=4518)





把$m^2$乘进去，答案其实就是$m\sum a_i^2-S_n^2$

其中$a_i$是第i天走的路程

那么就是一个最显然的平方和模型，直接斜率优化DP



示例程序：

```C++
#include<cstdio>
#include<cstring>
#include<algorithm>
#define cl(x,y) memset(x,y,sizeof(x))
using namespace std;
typedef long long ll;

const int maxn=3005;
int n,m,a[maxn],s[maxn],f[maxn][maxn];
struct point{
	ll x,y;
	point () {}
	point (ll _x,ll _y):x(_x),y(_y) {}
	inline int getb(int k){	return y-k*x;}
}stk[maxn];
typedef point vec;
vec operator-(const point&a,const point&b){
	return vec(a.x-b.x,a.y-b.y);
}
inline ll cross(vec a,vec b){
	return a.x*b.y-a.y*b.x;
}
int len,now;
int main(){
	scanf("%d%d",&n,&m);
	for (int i=1;i<=n;i++) scanf("%d",&a[i]),s[i]=s[i-1]+a[i];
	cl(f,63);
	f[0][0]=0;
	for (int j=1;j<=m;j++){
		len=0;now=1;
		for (int k=0;k<=n;k++){
			point a(s[k],f[k][j-1]+s[k]*s[k]);
			while (len>1&&cross(stk[len]-stk[len-1],a-stk[len-1])<=0) len--;
			stk[++len]=a;
		}
		for (int i=1;i<=n;i++){
			int k=2*s[i];
			while (now<len&&stk[now].getb(k)>stk[now+1].getb(k)) now++;
			f[i][j]=stk[now].getb(k)+s[i]*s[i];
		}
	}
	printf("%lld",(ll)f[n][m]*m-s[n]*s[n]);
	return 0;
}
```

