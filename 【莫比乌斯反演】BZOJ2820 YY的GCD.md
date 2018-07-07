# 【莫比乌斯反演】BZOJ2820 YY的GCD

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2820)



与[这道题](http://blog.csdn.net/linkfqy/article/details/78639272)类似。

先考虑枚举质数p，答案就是：
$$
\sum_p \sum_d \mu(d) \lfloor \frac n {pd} \rfloor \lfloor \frac m {pd} \rfloor
$$
设$T=pd$，考虑枚举$T$，则有：
$$
\sum_T^{min\{ n,m \}} \lfloor \frac n T \rfloor \lfloor \frac m T \rfloor \sum_{p|T} \mu\Big(\frac T p\Big)
$$
如果能够预处理$\sum_{p|T} \mu\Big(\frac T p\Big)$关于$T$的前缀和，前面的柿子就可以$O(\sqrt n)$求解

其实直接暴枚$p$就好了

因为均摊每个质数是$ln(n)$的，而质数的个数是$\frac n {ln(n)}$个

所以预处理是$O(n)$的



示例程序：

```C++
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;

const int maxn=10000005;
int tst,n,m,p[maxn],mu[maxn],t[maxn];
ll sum[maxn];
bool vis[maxn];
void get_mu(){
	int n=10000000;mu[1]=1;
	for (int i=2;i<=n;i++){
		if (!vis[i]) p[++p[0]]=i,mu[i]=-1;
		for (int j=1;j<=p[j]&&i*p[j]<=n;j++){
			vis[i*p[j]]=1;
			if (i%p[j]==0) {mu[i*p[j]]=0;break;}
			 else mu[i*p[j]]=-mu[i];
		}
	}
	for (int i=1;i<=p[0];i++)
	 for (int j=1;p[i]*j<=n;j++) t[p[i]*j]+=mu[j];
	for (int i=1;i<=n;i++) sum[i]=sum[i-1]+t[i];
}
ll get(int n,int m){
	if (n>m) swap(n,m);
	ll res=0;
	for (int T=1,lst=0;T<=n;T=lst+1){
		lst=min(n/(n/T),m/(m/T));
		res+=(sum[lst]-sum[T-1])*(n/T)*(m/T);
	}
	return res;
}
int main(){
	scanf("%d",&tst);get_mu();
	while (tst--){
		scanf("%d%d",&n,&m);
		printf("%lld\n",get(n,m));
	}
	return 0;
}
```

