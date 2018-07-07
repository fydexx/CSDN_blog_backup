# 【分块】BZOJ4216 Pig

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=4216)



典型的分块题……



没什么好说的，直接分块搞就好了



示例程序：

```C++
#include<cstdio>
#include<cmath>
#include<algorithm>
#define _abs(x) ((x)<0?-(x):(x))
#define LL long long
using namespace std;

const int maxn=500005;
int n,q,t,a[maxn],blk;
LL sum[710];
int main(){
	scanf("%d%d%d",&n,&q,&t);
	blk=sqrt(n);
	for (int i=1;i<=n;i++)
	 scanf("%d",&a[i]),sum[i/blk]+=a[i];
	LL lastans=0;
	for (int i=1;i<=q;i++){
		int l,r;scanf("%d%d",&l,&r);
		if (t==1){
			l=(l^_abs(lastans))%n+1;
			r=(r^_abs(lastans))%n+1;
			if (l>r) swap(l,r);
		}
		int bl=l/blk,br=r/blk;LL ans=0;
		if (bl==br){
			for (int j=l;j<=r;j++) ans+=a[j];
		}else{
			for (int j=l,tj=(bl+1)*blk;j<tj;j++) ans+=a[j];
			for (int j=br*blk;j<=r;j++) ans+=a[j];
			for (int j=bl+1;j<br;j++) ans+=sum[j];
		}
		printf("%lld\n",ans);lastans=ans;
	}
	return 0;
} 
```

