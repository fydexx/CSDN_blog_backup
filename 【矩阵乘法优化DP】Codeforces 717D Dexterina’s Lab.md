#【矩阵乘法优化DP】Codeforces 717D Dexterina’s Lab

[题面在这里](http://codeforces.com/problemset/problem/717/D)



首先要知道Nim游戏的结论：当前局面所有堆的异或和为0则先手必败

其实就是要求异或和不为0的概率

$f_{i,j}$表示前i堆，异或和为j的概率

转移矩阵非常优美：$T_{i,j}=p(i\oplus j)$



注意异或和有可能大于x，为此WA了一发……好不爽



示例程序：

```c++
#include<cstdio>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))

const int maxn=135;
int n,x;
double p[maxn];
struct matrix{
	double s[maxn][maxn];
	int n,m;
}t,a,res;
matrix operator*(const matrix&a,const matrix&b){
	matrix c;cl(c.s,0);
	c.n=a.n;c.m=b.m;
	for (int i=0;i<=a.n;i++)
	 for (int j=0;j<=b.m;j++)
	  for (int k=0;k<=a.m;k++)
	   c.s[i][j]+=a.s[i][k]*b.s[k][j];
	return c;
}
matrix power(matrix a,int b){
	matrix res;res.n=res.m=127;cl(res.s,0);
	for (int i=0;i<=127;i++) res.s[i][i]=1;
	while (b){
		if (b&1) res=res*a;
		a=a*a;
		b>>=1;
	}
	return res;
}
int main(){
	scanf("%d%d",&n,&x);
	for (int i=0;i<=x;i++) scanf("%lf",&p[i]);
	t.n=t.m=127;
	for (int i=0;i<=127;i++)
	 for (int j=0;j<=127;j++)
	  t.s[i][j]=p[i^j];
	a.n=127;a.m=0;
	a.s[0][0]=1;
	res=power(t,n)*a;
	printf("%.8lf",1-res.s[0][0]);
	return 0;
}
```

