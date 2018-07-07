[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2208)

首先$O(nm)$的暴力貌似能过？

一眼就看到了是传递闭包问题
定义 $f[i][j]$表示 $i$是否能到 $j$

问题在于怎么转移这个递推
可以用Tarjan缩点后按拓扑序递推，最坏是$O(\frac {n^2} {32})$
当然了……对于我这种懒人，最适合的还是Floyd大法
然后就变成了 $O(\frac {n^3} {32})$……

示例程序：

```
#include<cstdio>
#include<bitset>
using namespace std;
const int maxn=2005;
int n,ans;
char s[maxn];
bitset<maxn> f[maxn];
int main(){
	scanf("%d",&n);
	for (int i=1;i<=n;i++){
		scanf("%s",s);
		for (int j=1;j<=n;j++)
		 if (s[j-1]=='1'||i==j) f[i][j]=1;
	}
	for (int i=1;i<=n;i++)
	 for (int j=1;j<=n;j++)
	  if (f[j].test(i)) f[j]|=f[i];
	for (int i=1;i<=n;i++) ans+=f[i].count();
	printf("%d",ans);
	return 0;
}
```

