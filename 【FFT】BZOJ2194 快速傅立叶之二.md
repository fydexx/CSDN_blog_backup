#【FFT】BZOJ2194 快速傅立叶之二

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2194)

FFT经典题……

题目中$C_i$与$A_i,B_i$的关系非常奇怪，导致不能直接使用FFT加速
如果我们把$A_i$倒过来，那么就有：
$$
C_k=\sum_{i\ge k}^{i<n} a_{n-i-1}\cdot b_{i-k}
$$
所以就可以用FFT加速运算了

示例程序：
```
#include<cstdio>
#include<cmath>
#include<algorithm>
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

const int maxn=262150;
const double PI=3.14159265358979323846;
int n,a[maxn],b[maxn];
struct comp{
	double r,i;
	comp(double _r=0,double _i=0):r(_r),i(_i) {}
	comp operator + (const comp&b) {return comp(r+b.r,i+b.i);}
	comp operator - (const comp&b) {return comp(r-b.r,i-b.i);}
	comp operator * (const comp&b) {return comp(r*b.r-i*b.i,r*b.i+i*b.r);}
	comp operator / (const double&b) {return comp(r/b,i/b);}
}A[maxn],B[maxn],C[maxn];
int rev[maxn];
void get_rev(int n){
	rev[0]=0;
	for (int i=1,l=log2(n);i<n;i++)
	 rev[i]=(rev[i>>1]>>1)|((i&1)<<l-1);
}
void FFT(comp *a,int n,int d){
	for (int i=0;i<n;i++) if (rev[i]<i) swap(a[rev[i]],a[i]);
	for (int l=2;l<=n;l<<=1){
		comp w_n(cos(2*PI/l),sin(2*PI/l)*d);
		for (int k=0;k<n;k+=l){
			comp w(1,0),_t,_T;
			for (int j=0,tj=l>>1;j<tj;j++,w=w*w_n)
			 _t=a[k+j],_T=w*a[k+j+tj],
			 a[k+j]=_t+_T,a[k+j+tj]=_t-_T;
		}
	}
	if (d<0) for (int i=0;i<n;i++) a[i]=a[i]/n;
}
int main(){
	n=red();
	for (int i=0;i<n;i++) a[n-i-1]=red(),b[i]=red();
	for (int i=0;i<n;i++) A[i].r=a[i],B[i].r=b[i];
	int t=n;n=1;while (n<t*2) n<<=1;
	get_rev(n);
	FFT(A,n,1);FFT(B,n,1);
	for (int i=0;i<n;i++) C[i]=A[i]*B[i];
	FFT(C,n,-1);
	for (int i=0;i<t;i++) printf("%d\n",(int)(C[t-i-1].r+0.5));
	return 0;
}
```