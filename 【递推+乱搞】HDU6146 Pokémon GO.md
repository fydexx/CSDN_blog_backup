#【递推+乱搞】HDU6146 Pokémon GO

[题面在这里](http://acm.hdu.edu.cn/showproblem.php?pid=6146)

要使步数最少，则每个格子只走一次
定义$A_i$表示$2\times i$的矩阵，从某个角落出发遍历整个矩阵的方案数
$B_i$表示$2\times i$的矩阵，从某个角落出发遍历整个矩阵，并回到出发的那列的方案数

显然有：
$$
B_i=2^{i-1}
$$
$$
A_i=B_i+2\cdot A_{i-1}+4\cdot A_{i-2}
$$

然后如果从角落出发，答案是$4\cdot A_n$
枚举在哪一列出发，对答案的贡献是$8\times (A_{i-1}B_{n-i}+B_{i-1}A_{n-i})$

示例程序：

```
#include<cstdio>
#define LL long long

const int maxn=10005,tt=1000000007;
int tst,n;
LL a[maxn],b[maxn];
int main(){
	n=10000;b[1]=1;a[1]=1;a[2]=6;
	for (int i=2;i<=n;i++) b[i]=b[i-1]*2%tt;
	for (int i=3;i<=n;i++) a[i]=(b[i]+a[i-1]*2+a[i-2]*4)%tt;
	scanf("%d",&tst);
	while (tst--){
		scanf("%d",&n);
		if (n==1) {printf("2\n");continue;}
		LL ans=a[n]*4%tt;
		for (int i=2;i<n;i++) ans=(ans+8*(a[i-1]*b[n-i]%tt + b[i-1]*a[n-i]%tt))%tt;
		printf("%lld\n",ans);
	}
	return 0;
}
```