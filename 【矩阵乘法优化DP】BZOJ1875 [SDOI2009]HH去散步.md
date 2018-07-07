# 【矩阵乘法优化DP】BZOJ1875 [SDOI2009]HH去散步

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1875)



典型的矩阵乘法优化DP

重点在于如何避免走回头路

如果记点为状态的话肯定不好搞

所以定义$f_{i,j}$表示 走了i步，走到j这条边的终点 的方案数

若边i的终点与边j的起点相同，则$f_{t,i}$可以转移到$f_{t+1,j}$

由此构造转移矩阵



示例程序：

```C++
#include<cstdio>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))

const int maxe=125,tt=45989;
int n,m,k,S,T,ans;
struct data{
	int s,t,id;
	data () {}
	data (int _s,int _t,int _id):s(_s),t(_t),id(_id) {}
}edge[maxe];
struct matrix{
	int n,m,s[maxe][maxe];
}t,a,res;
matrix operator*(const matrix&a,const matrix&b){
	matrix c;cl(c.s,0);
	c.n=a.n;c.m=b.m;
	for (int i=1;i<=c.n;i++)
	 for (int j=1;j<=c.m;j++)
	  for (int k=1;k<=a.m;k++)
	   (c.s[i][j]+=a.s[i][k]*b.s[k][j]%tt)%=tt;
	return c;
}
matrix power(matrix a,int b){
	matrix w=a,ans;
	ans.n=ans.m=m;cl(ans.s,0);
	for (int i=1;i<=m;i++) ans.s[i][i]=1;
	while (b){
		if (b&1) ans=ans*w;
		w=w*w;
		b>>=1;
	}
	return ans;
}
int main(){
	scanf("%d%d%d%d%d",&n,&m,&k,&S,&T);
	for (int i=1;i<=m;i++){
		int x,y;scanf("%d%d",&x,&y);
		edge[i*2-1]=data(x,y,i);edge[i*2]=data(y,x,i);
	}m<<=1;
	t.n=t.m=m;
	for (int i=1;i<=m;i++)
	 for (int j=1;j<=m;j++)
	  if (edge[i].id!=edge[j].id)
	   if (edge[i].t==edge[j].s) t.s[j][i]++;
	a.n=m;a.m=1;
	for (int i=1;i<=m;i++)
	 if (edge[i].s==S) a.s[i][1]=1;
	res=power(t,k-1)*a;
	for (int i=1;i<=m;i++)
	 if (edge[i].t==T) (ans+=res.s[i][1])%=tt;
	printf("%d",ans);
	return 0;
}
```

