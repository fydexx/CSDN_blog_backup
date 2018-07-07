#【DP】RQNOJ #107 Ural的鹰蛋实验

[题面在这里](http://www.rqnoj.cn/problem/107)



实在不懂为什么如此经典的题目只能在这种SBOJ上做……

显然可以这样DP：

$f_{i,j}$表示有i个蛋，要判断j层楼的最少次数

枚举在哪一层楼扔鸡蛋
$$
f_{i,j}=Min\{ Max\{f_{i-1,k-1},f_{i,j-k} \}+1 \}\ \ \ \ \ \ \ \ \  \left( 1\le k\le j \right)
$$
但是这样时$O(nm^2)$的，效率太低了

可以发现最多需要$\lceil log_2^m \rceil$个鸡蛋

所以状态数只有$O(m\cdot logm)$了



示例程序：

```c++
#include<cstdio>
#include<cmath>
#include<cstring>
#include<algorithm>
#define cl(x,y) memset(x,y,sizeof(x))
using namespace std;

const int maxn=1005,INF=0x3f3f3f3f;
int n,m,f[maxn][maxn];
int dfs(int n,int m){
	if (f[n][m]!=INF) return f[n][m];
	int l=log2(m)+1-1e-7;if (n>=l) return l;
	for (int i=1;i<=m;i++)
	 f[n][m]=min(f[n][m],max(dfs(n-1,i-1),dfs(n,m-i))+1);
	return f[n][m];
}
int main(){
	scanf("%d%d",&n,&m);
	cl(f,63);
	for (int i=1;i<=n;i++) f[i][0]=0;
	for (int j=1;j<=m;j++) f[1][j]=j;
	printf("%d",dfs(n,m));
	return 0;
}
```

