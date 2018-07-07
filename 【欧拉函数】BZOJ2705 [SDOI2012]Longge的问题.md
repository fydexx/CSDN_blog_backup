#【欧拉函数】BZOJ2705 [SDOI2012]Longge的问题

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2705)



考虑如下：
$$
\sum_{i=1}^n (i,n)  \\
=\sum_{d|n}d\sum_{id\le n} [(id,n)=d]  \\
=\sum_{d|n}d\sum_{i=1}^{\frac n d} [(i,\frac n d)=1]  \\
=\sum_{d|n}d\cdot\varphi(\frac n d)
$$
然后直接暴力就好了



示例程序：

```C++
#include<cstdio>
#include<cmath>
typedef long long ll;

ll n,N,ans;
ll phi(int x){
	int res=x,ti=sqrt(x);
	for (int i=2;i<=ti;i++)
	 if (x%i==0){
	 	res=res/i*(i-1);
	 	while (x%i==0) x/=i;
	 }
	if (x>1) res=res/x*(x-1);
	return res;
}
int main(){
	scanf("%lld",&n);N=sqrt(n);
	for (int i=1;i<=N;i++)
	 if (n%i==0){
		ans+=i*phi(n/i);
		if ((ll)i*i<n) ans+=(n/i)*phi(i);
	 }
	printf("%lld",ans);
	return 0;
}
```

