# 【数学乱搞】HDU1568 Fibonacci

[题面在这里](http://acm.hdu.edu.cn/showproblem.php?pid=1568)



Lynstery大佬说这是傻逼题……



首先考虑斐波那契的通项公式：
$$
F_n=\frac 1 {\sqrt 5} \left[ \left(\frac {1+\sqrt 5}2\right)^n- \left( \frac {1-\sqrt 5}2\right)^n \right] \\
=\frac 1 {\sqrt 5} \left(\frac {1+\sqrt 5}2\right)^n\left[1- \left( \frac {1-\sqrt 5}{1+\sqrt 5}\right)^n \right] \\
log_{10}(F_n)=log_{10}\left(\frac 1 {\sqrt 5} \right)+n\cdot log_{10}\left(\frac {1+\sqrt 5}2\right)+ log_{10}\left[1- \left( \frac {1-\sqrt 5}{1+\sqrt 5}\right)^n \right]
$$
然后当n很大时，最后一项可以忽略不计

所以就很好做了：

考虑将$F_n$表示成科学计数法：
$$
F_n=1.balabala\times 10^k \\
log_{10}(F_n)=log_{10}(1.balabala)+k
$$
此时做法已经很显然了：先得到$1.balabala=10^{log_{10}(F_n) - \left \lfloor log_{10}(F_n) \right \rfloor }$

然后一直乘以10，直到得到四位数



注：当n很小时，不太精确，所以要预处理前20个

示例程序：

```C++
#include<cstdio>
#include<cmath>
typedef long long LL;

const double s=1.6180339887498948482045868343656;
int n;
LL f[25];
int main(){
	f[0]=0;f[1]=1;
	for (int i=2;i<=20;i++) f[i]=f[i-1]+f[i-2];
	while (~scanf("%d",&n)){
		if (n<=20) {printf("%d\n",f[n]);continue;}
		double ans=-0.5*log10(5)+n*log10(s);
		ans-=(int)ans;
		ans=pow(10,ans);
		while (ans<1000) ans*=10;
		printf("%d\n",(int)ans);
	}
	return 0;
}
```

