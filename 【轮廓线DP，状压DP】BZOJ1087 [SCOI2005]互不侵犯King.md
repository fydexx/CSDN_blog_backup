[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1087)

轮廓线DP的经典题……

可以发现，对于当前点(i,j)，只有前面n+1个格子与其有关（如下图）
![这里写图片描述](http://img.blog.csdn.net/20170609075455946?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
那么这n+1个位置就是(i,j)的轮廓线。
把轮廓线上的状态用位运算压缩一下即可。

$f[c][s][k]$表示位置c（滚动数组优化空间），轮廓线上状态s，已经放了k个王的方案数
那么显然，若(i,j)不放王：

```
f[c][ss][k]+=f[c^1][s][k];
```

（ss是新轮廓线的状态）
若(i,j)放王，就要让(i-1,j-1)、(i-1,j)、(i-1,j+1)、(i,j-1)都没有王：

```
if (k<K&&check(i,j,s)) f[c][ss+1][k+1]+=f[c^1][s][k];
```
那么就完成了

附上代码：
```
#include<cstdio>
#include<cstring>
#define LL long long
const int maxs=1030,maxk=105;
int n,K;
LL f[2][maxs][maxk],ans=0;
bool check(int i,int j,int s){
	if (i>1&&j>1&&(s&(1<<n))) return 0;
	if (i>1&&(s&(1<<n-1))) return 0;
	if (i>1&&j<n&&(s&(1<<n-2))) return 0;
	if (j>1&&(s&1)) return 0;
	return 1;
}
int main(){
	scanf("%d%d",&n,&K);
	int c=0;f[0][0][0]=1;
	for (int i=1;i<=n;i++)
	 for (int j=1;j<=n;j++){
	 	c^=1;memset(f[c],0,sizeof(f[c]));
	 	for (int s=0;s<(1<<n+1);s++){
	 		int ss=(s<<1)%(1<<n+1);
	 		for (int k=0;k<=K;k++){
	 			f[c][ss][k]+=f[c^1][s][k];
	 			if (k<K&&check(i,j,s)) f[c][ss+1][k+1]+=f[c^1][s][k];
	 		}
	 	}
	 }
	for (int s=0;s<(1<<n+1);s++) ans+=f[c][s][K];
	printf("%lld",ans);
	return 0;
}
```