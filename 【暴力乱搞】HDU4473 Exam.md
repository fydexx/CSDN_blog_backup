[题面在这里](http://acm.hdu.edu.cn/showproblem.php?pid=4473)

先规定$a\le b\le c$
考虑暴力枚举a和b
那么c的取值一定是一个区间
然后讨论一下相同值的情况即可

复杂度显然是$O(n^{\frac 2 3})$

示例程序：

```
#include<cstdio>
#define LL long long

LL n;
int main(){
	int i=0;
	while (~scanf("%lld",&n)){
		LL ans=0;
		for (LL i=1;i*i*i<=n;i++)
		 for (LL j=i;j*j*i<=n;j++){
		 	LL num=n/(i*j)-j+1;
		 	if (i==j) ans+=num*3-2;else
		 	 ans+=num*6-3;
		 }
		printf("Case %d: %lld\n",++i,ans);
	}
	return 0;
}
```