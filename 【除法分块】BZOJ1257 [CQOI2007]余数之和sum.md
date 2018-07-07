# 【除法分块】BZOJ1257 [CQOI2007]余数之和sum

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1257)

把答案的形式写出来就是这样的：
$$
\sum_{i=1}^m n-\lfloor \frac n i \rfloor \cdot i
$$
$$
nm-\sum_{i=1}^m \lfloor \frac n i \rfloor \cdot i
$$
可以发现，随着i的增长，$\lfloor \frac n i \rfloor$是可以分块的
而且最多有$O(\sqrt n)$级别的块数

示例程序：
```
#include<cstdio>
#include<cmath>
#define LL long long

LL n,nn,m,ans,a[3162300];
int main(){
	scanf("%lld%lld",&m,&n);nn=sqrt(n);
	ans=n*(m);
	for (int k=1;k<=nn;k++){
		a[k]=n/k;
		LL l=n/(k+1)+1,r=n/k;
		if (l>m) continue;
		if (r>m) r=m;
		ans-=(k*((l+r)*(r-l+1)/2));
	}
	for (int i=1;i<nn;i++){
		LL k=a[i],l=n/(k+1)+1,r=n/k;
		if (l>m) continue;
		if (r>m) r=m;
		ans-=(k*((l+r)*(r-l+1)/2));
	}
	if (n/nn!=nn){
		LL k=a[nn],l=n/(k+1)+1,r=n/k;
		ans-=(k*((l+r)*(r-l+1)/2));
	}
	printf("%lld",ans);
	return 0;
}
```