# 【莫比乌斯反演】BZOJ1101 [POI2007]Zap

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1101)



没什么好说的，反演裸题



示例程序：

```C++
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;

const int maxn=50005,N=maxn-5;
int q,K,mu[maxn],sum[maxn],p[maxn];
bool vis[maxn];
void prepare(){
	mu[1]=1;
	for (int i=2;i<=N;i++){
		if (!vis[i]) p[++p[0]]=i,mu[i]=-1;
		for (int j=1;j<=p[0],i*p[j]<=N;j++){
			vis[i*p[j]]=1;
			if (i%p[j]==0) {mu[i*p[j]]=0;break;}
			 else mu[i*p[j]]=-mu[i];
		}
	}
	for (int i=1;i<=N;i++) sum[i]=sum[i-1]+mu[i];
}
ll get(int n,int m){
	n/=K;m/=K;
	if (n>m) swap(n,m);
	ll res=0;
	for (int d=1,lst=0;d<=n;d=lst+1){
		lst=min(n/(n/d),m/(m/d));
		res+=(ll)(sum[lst]-sum[d-1])*(n/d)*(m/d);
	}
	return res;
}
int main(){
	scanf("%d",&q);
	prepare();
	while (q--){
		int a,b;
		scanf("%d%d%d",&a,&b,&K);
		printf("%lld\n",get(a,b));
	}
	return 0;
}
```

