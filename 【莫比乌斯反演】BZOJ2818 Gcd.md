# 【莫比乌斯反演】BZOJ2818 Gcd

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2818)



反演裸题不解释



示例程序：

```C++
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long ll;

const int maxn=10000005;
int n,p[maxn],mu[maxn],N=maxn-5;
ll f[maxn];
bool vis[maxn];
void prepare(){
	mu[1]=1;N=min(N,n);
	for (int i=2;i<=N;i++){
		if (!vis[i]) p[++p[0]]=i,mu[i]=-1;
		for (int j=1;j<=p[0]&&i*p[j]<=N;j++){
			vis[i*p[j]]=1;
			if (i%p[j]==0) {mu[i*p[j]]=0;break;}
			 else mu[i*p[j]]=-mu[i];
		}
	}
	for (int i=1;i<=p[0];i++)
	 for (int j=1;p[i]*j<=N;j++) f[p[i]*j]+=mu[j];
	for (int i=1;i<=N;i++) f[i]+=f[i-1];
}
int main(){
	scanf("%d",&n);ll ans=0;
	prepare();
	for (int d=1,lst=0;d<=n;d=lst+1){
		lst=n/(n/d);
		ans+=(ll)(f[lst]-f[d-1])*(n/d)*(n/d);
	}
	printf("%lld",ans);
	return 0;
}
```

