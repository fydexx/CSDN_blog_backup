# 【KMP+DP+矩阵优化】BZOJ1009 [HNOI2008]GT考试

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1009)



显然需要递推，定义$f_{i,j}$为处理到第i位，不吉利数字匹配到第j位的方

显然答案就是
$$
\sum_{i=0}^{m-1} f_{n,i}
$$
怎么转移状态？

对于$f_{i,j}$，枚举i+1位的数字，用KMP得到不吉利数字新的匹配位置k，就有
$$
f_{i+1,k}+=f_{i,j}
$$
然而n是$10^9$级别的，显然不能这样搞

发现这个递推式是线性的，那么用矩阵优化就好了

  

  

我们必然能找到一个矩阵A，使得：
$$
A\times\begin{bmatrix}f_{i,0}\\f_{i,1}\\f_{i,2}\\...\\f_{i,m}\end{bmatrix}=\begin{bmatrix}f_{i+1.0}\\f_{i+1,1}\\f_{i+1,2}\\...\\f_{i+1,M}\end{bmatrix}
$$
如何构造矩阵A？

考虑矩阵$A_{x,y}$表示$f_{i,y}$对$f_{i+1,x}$的贡献

那么就枚举y和i+1这位的数字，用KMP得到x，然后累加



答案矩阵就是
$$
A^n\times\begin{bmatrix}1\\0\\0\\...\\0\end{bmatrix}
$$
示例程序：

```C++
#include<cstdio>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))

const int maxm=25;
int n,m,p,fal[maxm];
char s[maxm];
struct matrix{
	int n,m,s[maxm][maxm];
	matrix operator*(const matrix &b){
		matrix c;cl(c.s,0);
		c.n=n;c.m=b.m;
		for (int i=0;i<=c.n;i++)
		 for (int j=0;j<=c.m;j++){
		 	c.s[i][j]=0;
		 	for (int k=0;k<=m;k++)
			 c.s[i][j]+=s[i][k]*b.s[k][j];
			c.s[i][j]%=p;
		 }
		return c;
	}
}A,ans;
matrix power(matrix a,int b){
	matrix w=a,ans;ans.n=a.n;ans.m=a.m;
	cl(ans.s,0);for (int i=0;i<=m;i++) ans.s[i][i]=1;
	while (b){
		if (b&1) ans=ans*w;
		w=w*w;
		b>>=1;
	}
	return ans;
}
int main(){
	scanf("%d%d%d%s",&n,&m,&p,s+1);
	fal[1]=0;
	for (int i=2,j=0;i<=m;i++){
		while (j&&s[j+1]!=s[i]) j=fal[j];
		if (s[j+1]==s[i]) j++;
		fal[i]=j;
	}
	A.n=A.m=m;
	for (int y=0;y<m;y++)
	 for (char i='0';i<='9';i++){
	 	int j=y;
	 	while (j&&s[j+1]!=i) j=fal[j];
	 	if (s[j+1]==i) j++;
	 	if (j<m) A.s[j][y]++;
	 }
	ans.n=m;ans.m=0;ans.s[0][0]=1;
	ans=power(A,n)*ans;
	int res=0;
	for (int i=0;i<m;i++) res=(res+ans.s[i][0])%p;
	printf("%d",res);
	return 0;
}
```

