# 【期望DP】LOJ#6178. 「美团 CodeM 初赛 Round B」景区路线规划

[题面在这里](https://loj.ac/problem/6178)



这题一定要倒着处理！！！

定义$f_{i,j}$表示时间为i，位置是j，走到不能走的期望

为什么要这样倒着定义呢？

因为只有这样才能保证默认当前状态的概率是1

正着做就比较烦了

然后……$f_{i,j}\rightarrow f_{i+w_i,k}$



示例程序：

```C++
#include<cstdio>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))

const int maxn=105,maxe=10005;
int n,m,K,c[maxn],h[2][maxn];
double f[485][maxn];
int tot,son[maxe],nxt[maxe],lnk[maxn],w[maxe];
inline void add(int x,int y,int z) {
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;w[tot]=z;
}
double work(int t){
	double ans=0;cl(f,0);
	for (int i=K;i;i--)
	 for (int j=1;j<=n;j++){
	 	int s=0;
	 	for (int k=lnk[j];k;k=nxt[k])
	 	 if (i+w[k]<=K) s++;
	 	if (s==0) {f[i][j]=h[t][j];continue;}
	 	for (int k=lnk[j];k;k=nxt[k])
	 	 if (i+w[k]<=K) f[i][j]+=f[i+w[k]][son[k]]/s;
	 	f[i][j]+=h[t][j];
	 }
	for (int i=1;i<=n;i++) ans+=f[c[i]][i]/n;
	return ans;
}
int main(){
	scanf("%d%d%d",&n,&m,&K);
	for (int i=1;i<=n;i++) scanf("%d%d%d",&c[i],&h[0][i],&h[1][i]);
	for (int i=1,x,y,z;i<=m;i++) scanf("%d%d%d",&x,&y,&z),add(x,y,z+c[y]),add(y,x,z+c[x]);
	printf("%.5lf %.5lf",work(0),work(1));
	return 0;
}
```

