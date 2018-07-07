[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2813)

一道很好的数论题……让人思考很多……

参考博客：[斐波那契数列的性质](http://blog.csdn.net/linkfqy/article/details/73863798)
首先有这个公式：
$$n|m\Leftrightarrow f_n|f_m$$
那么题目就转化为：询问i有多少个因子，以及所有因子的平方和

可以用欧拉筛一边处理一边统计：
$e_i$表示i的最小质因数的次数
$d_i$表示i除去最小质因数后的数
$g_i$表示i的因子个数
$f_i$表示i的因子的平方和

在筛素数的时候：
记$i\cdot p_j=k$

1.若 $i\% p_j>0$，说明i之前没有$p_j$这个因子
根据欧拉筛的性质，$p_j$一定是k最小的质因数
那么：
$e_k=1,d_k=i$
$g_k=2g[i]$
$f_k=p_j^2f_+f_i$
（把因数分成含$p_j$和不含$p_j$的来考虑）

2.若 $i\% p_j=0$，说明i之前已经出现过$p_j$这个因子
同样，把$k$的因数看为含$p_j$和不含$p_j$两类考虑，可得：
$e_k=e_i+1,d_k=d_i$
$g_k=g_i/(e_i+1)*(e_k+1)$
$f_k=f_i*p_j^2+f_{d_i}$

但是要注意：
$n|m\Leftrightarrow f_n|f_m$
这个性质有一个特殊情况，就是$f_2=1$
如果询问的i是奇数，以上算法会认为$f_2$不是$f_i$的因子
那么统计答案的时候特判一下即可

附上代码：

```
#include<cstdio>
#define LL long long
const int maxn=10000005,tt=1000000007;
int q,now,A,B,C,sumg,sumf;
int e[maxn],d[maxn],g[maxn],f[maxn],p[maxn];
bool vis[maxn];
LL sqr(int x) {return (LL)x*x;}
int main(){
	scanf("%d%d%d%d%d",&q,&now,&A,&B,&C);
	d[1]=g[1]=f[1]=1;
	for (int i=2;i<=C;i++){
		if (!vis[i])
		 p[++p[0]]=i,
		 e[i]=d[i]=1,g[i]=2,
		 f[i]=(sqr(i)+1)%tt;
		for (int j=1,k;j<=p[0]&&(k=i*p[j])<=C;j++){
			vis[k]=1;
			if (i%p[j]>0)
			 e[k]=1,d[k]=i,
			 g[k]=(g[i]+g[i])%tt,
			 f[k]=(sqr(p[j])+1)*f[i]%tt;
			else{
				e[k]=e[i]+1,d[k]=d[i],
				g[k]=(LL)g[i]/(e[i]+1)*(e[k]+1)%tt,
				f[k]=(f[i]*sqr(p[j])+f[d[i]])%tt;
				break;
			}
		}
	}
	sumg=sumf=0;
	while (q--){
		(sumg+=g[now]+(now&1))%=tt;
		(sumf+=f[now]+4*(now&1))%=tt;
		now=((LL)now*A+B)%C+1;
	}
	printf("%d\n%d",sumg,sumf);
	return 0;
}
```