# 【斜率优化DP】BZOJ1911 [Apio2010]特别行动队

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1911)



又是一道最简单的斜率优化……

如下推导：
$$
f_i=Max\{ f_j+a(S_i-S_j)^2+b(S_i-S_j)+c \} \\
f_i=Max\{ f_j+aS_j^2-bS_j-2aS_iS_j \} +c+aS_i^2-bS_j \\
\text{设}k=2aS_i,x=S_j,y=f_j+aS_j^2-bS_j \space \text{则：} \\
b=y-kx \\
y=kx+b
$$
然后就好了，此题唯一要注意的地方是：斜率k是递减而非递增的，而且要求最大值

维护上突壳即可

示例程序：

```C++
#include<cstdio>
#define sqr(x) ((x)*(x))
#define max(x,y) ((x)>(y)?(x):(y))
typedef long long LL;
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

const int maxn=1000005;
int n,len,now;
LL a,b,c,s[maxn],ans;
struct point{
	LL x,y;
	point () {}
	point (LL _x,LL _y):x(_x),y(_y) {}
}stk[maxn];
typedef point vec;
vec operator-(const vec&a,const vec&b){
	return vec(a.x-b.x,a.y-b.y);
}
LL cross(vec a,vec b){
	return a.x*b.y-a.y*b.x;
}
LL getb(point a,LL k){
	return a.y-k*a.x;
}
LL get(LL k){
	while (now<len&&getb(stk[now],k)<getb(stk[now+1],k)) now++;
	return getb(stk[now],k);
}
void put(point a){
	while (len>1&&cross(stk[len]-stk[len-1],a-stk[len-1])>0) len--;
	if (now>len) now=len;stk[++len]=a;
}
int main(){
	n=red();a=red(),b=red(),c=red();
	for (int i=1;i<=n;i++) s[i]=s[i-1]+red();
	len=now=1;stk[1]=point(0,0);ans=0;
	for (int i=1;i<=n;i++){
		LL k=2*a*s[i],f=get(k)+c+a*sqr(s[i])+b*s[i];
		point p(s[i],f+a*sqr(s[i])-b*s[i]);
		put(p);ans=max(ans,f);
	}
	printf("%lld",ans);
	return 0;
}
```

