# 【FFT】BZOJ2179 FFT快速傅立叶

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2179)

FFT裸题……



双倍经验，但是为了巩固还是重新打了一遍



示例程序：

```C++
#include<cstdio>
#include<cmath>
#include<algorithm>
using namespace std;

const int maxn=131080;
const double PI=3.14159265358979323846;
int n,len;
char a[maxn],b[maxn];
struct comp{
	double r,i;
	comp (double _r=0,double _i=0):r(_r),i(_i) {}
	comp operator + (const comp&b) {return comp(r+b.r,i+b.i);}
	comp operator - (const comp&b) {return comp(r-b.r,i-b.i);}
	comp operator * (const comp&b) {return comp(r*b.r-i*b.i,r*b.i+i*b.r);}
	comp operator / (const double&b) {return comp(r/b,i/b);}
}A[maxn],B[maxn],C[maxn];
int rev[maxn];
void get_rev(int n){
	rev[0]=0;int l=log2(n);
	for (int i=1;i<n;i++)
	 rev[i]=(rev[i>>1]>>1)|((i&1)<<l-1);
}
void FFT(comp *a,int n,int d){
	for (int i=0;i<n;i++) if (rev[i]<i) swap(a[i],a[rev[i]]);
	for (int l=2;l<=n;l<<=1){
		comp w_n(cos(2*PI/l),d*sin(2*PI/l));
		for (int k=0;k<n;k+=l){
			comp w(1,0),_t,_T;
			for (int j=0,tj=l>>1;j<tj;j++,w=w*w_n)
			 _t=a[k+j],_T=w*a[k+j+tj],
			 a[k+j]=_t+_T,a[k+j+tj]=_t-_T;
		}
	}
	if (d<0) for (int i=0;i<n;i++) a[i]=a[i]/n;
}
int stk[maxn];
void write(comp *a,int n){
	int top=n;
	for (int i=0;i<n;i++)
	 stk[i]+=C[i].r+0.5,
	 stk[i+1]+=stk[i]/10,
	 stk[i]%=10;
	while (top>1&&stk[top]==0) top--;
	for (int i=top;i>=0;i--) putchar(stk[i]+48);
}
int main(){
	scanf("%d%s%s",&len,a,b);
	n=1;while (n<len*2) n<<=1;
	get_rev(n);
	for (int i=0;i<len;i++) A[len-i-1].r=a[i]-48,B[len-i-1].r=b[i]-48;
	FFT(A,n,1);FFT(B,n,1);
	for (int i=0;i<n;i++) C[i]=A[i]*B[i];
	FFT(C,n,-1);
	write(C,n);
	return 0;
}
```

