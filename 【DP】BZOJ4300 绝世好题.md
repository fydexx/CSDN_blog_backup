# 【DP】BZOJ4300 绝世好题

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=4300)



一个简单的DP。

$f_i$表示b序列最后一个数第i位为1的最长长度

然后XJB更新一下就好了



示例程序：

```C++
#include<cstdio>
#include<algorithm>
using namespace std;

int n,f[35];
int main(){
	scanf("%d",&n);
	for (int i=1;i<=n;i++){
		int x,Max=0;scanf("%d",&x);
		for (int j=0;j<=31;j++)
		 if ((x>>j)&1) Max=max(Max,f[j]+1);
		for (int j=0;j<=31;j++)
		 if ((x>>j)&1) f[j]=max(f[j],Max);
	}
	int ans=0;
	for (int i=0;i<=31;i++) ans=max(ans,f[i]);
	printf("%d",ans);
	return 0;
}
```

