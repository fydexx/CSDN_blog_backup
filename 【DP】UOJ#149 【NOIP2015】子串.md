# 【DP】UOJ#149 【NOIP2015】子串

[题面在这里](http://uoj.ac/problem/149)



其实就是DP

$f_{i,j,k}$表示A串处理到i，B串到j，分了k个块

发现状态数已经很大了，想办法$O(1)$转移

那么就$f_{i,j,k,0/1}$表示i是否在块里

然后就很好转移了



示例程序：

```C++
#include<cstdio>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))
typedef long long ll;

const int maxn=1005,maxm=205,tt=1000000007;
int n,m,K,f[2][maxm][maxm][2];
char a[maxn],b[maxm];
int main(){
	scanf("%d%d%d",&n,&m,&K);
	scanf("%s%s",a+1,b+1);
	f[0][0][0][0]=1;
	for (int i=1;i<=n;i++){
		cl(f[i&1],0);
		f[i&1][0][0][0]=1;
		for (int j=1;j<=m;j++)
		 for (int k=1;k<=K;k++){
		 	f[i&1][j][k][0]=(f[i&1^1][j][k][0]+f[i&1^1][j][k][1])%tt;
		 	if (a[i]==b[j]) f[i&1][j][k][1]=((ll)f[i&1^1][j-1][k-1][0]+f[i&1^1][j-1][k-1][1]+f[i&1^1][j-1][k][1])%tt;
		 }
	}
	printf("%d",(f[n&1][m][K][0]+f[n&1][m][K][1])%tt);
	return 0;
} 
```

