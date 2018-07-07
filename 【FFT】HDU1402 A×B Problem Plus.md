# 【FFT】HDU1402 A*B Problem Plus

[题面在这里](http://acm.hdu.edu.cn/showproblem.php?pid=1402)



FFT实现高精数乘……



没什么好讲的，直接上代码

```C++
#include<cstdio>
#include<cmath>
#include<cstring>
#include<algorithm>
#define cl(x,y) memset(x,y,sizeof(x))
using namespace std;

const int maxn=300005;
const double PI=3.14159265358979323846;
int n,lena,lenb,stk[maxn];
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
		comp wl(cos(2*PI/l),d*sin(2*PI/l));
		for (int k=0;k<n;k+=l){
			comp w(1,0),_t,_T;
			for (int j=0,tj=l>>1;j<tj;j++,w=w*wl)
			 _t=a[k+j],_T=w*a[k+j+tj],a[k+j]=_t+_T,a[k+j+tj]=_t-_T;
	 	}
	}
	if (d<0) for (int i=0;i<n;i++) a[i]=a[i]/n;
}
int main(){
	while (~scanf("%s%s",a,b)){
		lena=strlen(a);lenb=strlen(b);
		n=1;cl(A,0);cl(B,0);
		while (n<lena+lenb) n<<=1; n<<=1;
		for (int i=0;i<lena;i++) A[lena-i-1].r=a[i]-48;
		for (int i=0;i<lenb;i++) B[lenb-i-1].r=b[i]-48;
		get_rev(n);
		FFT(A,n,1);FFT(B,n,1);
		for (int i=0;i<n;i++) C[i]=A[i]*B[i];
		FFT(C,n,-1);cl(stk,0);
		for (int i=0;i<n;i++)
		 stk[i]+=C[i].r+0.5,
		 stk[i+1]+=stk[i]/10,
		 stk[i]%=10;
		bool fir=1;
		for (int i=n-1;i>=0;i--){
			if (stk[i]==0&&fir) continue;
			if (stk[i]!=0) fir=0;
			putchar(stk[i]+48);
		}
		if (fir) putchar('0');
		putchar('\n');
	}
	return 0;
} 
```
