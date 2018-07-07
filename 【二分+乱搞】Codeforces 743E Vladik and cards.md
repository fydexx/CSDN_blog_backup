#【二分+乱搞】Codeforces 743E Vladik and cards

[题面在这里](http://codeforces.com/problemset/problem/743/E)



发现子序列的长度是单调的，所以可以直接二分答案



如果答案确定了，那么每个数字$(1\text{~}8)$只能取$\lfloor \frac {mid} 8 \rfloor$或$\lfloor \frac {mid} 8 \rfloor+1$

再$8!$暴力枚举一下8个数字出现的顺序

然后直接乱搞就可以验证答案是否符合题意



复杂度：由于加了很多剪枝，复杂度远小于$O(nlogn\cdot 8!)$



示例程序：

```C++
#include<cstdio>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))
using namespace std;

const int maxn=1005;
int n,m,a[maxn],nxt[maxn],ans,p[40325][10],t,l;
bool vis[10];
void maker(int stp){
	if (stp>8){
		m++;
		for (int i=1;i<=8;i++) p[m][i]=a[i];
		return;
	}
	for (int i=1;i<=8;i++)
	 if (!vis[i]){
	 	vis[i]=1;a[stp]=i;
	 	maker(stp+1);
	 	vis[i]=0;
	 }
}
bool dfs(int stp,int i,int *p,int s){
	if (stp>8) return s==l;
	
	if (t==0) {if (dfs(stp+1,i,p,s)) return 1;}else{
		while (i<=n&&a[i]!=p[stp]) i++;
		for (int j=1;j<=t-1;j++) i=nxt[i];
		if (i>n) return 0;
		if (dfs(stp+1,i,p,s)) return 1;
	}
	
	while (i<=n&&a[i]!=p[stp]) i++;
	if (t>0) i=nxt[i];
	if (i>n) return 0;
	if (dfs(stp+1,i,p,s+1)) return 1;
	return 0;
}
bool check(int mid){
	t=mid/8;l=mid%8;
	for (int i=1;i<=m;i++)
	 if (dfs(1,1,p[i],0)) return 1;
	return 0;
}
int main(){
	scanf("%d",&n);maker(1);
	for (int i=1;i<=n;i++) scanf("%d",&a[i]);
	for (int i=1;i<=n;i++){
		for (int j=i+1;j<=n;j++)
		 if (a[j]==a[i]) {nxt[i]=j;break;}
		if (!nxt[i]) nxt[i]=n+1;
	}nxt[n+1]=n+1;
	int L=1,R=n;
	while (L<=R){
		int mid=L+R>>1;
		if (check(mid)) ans=mid,L=mid+1;else R=mid-1;
	}
	printf("%d",ans);
	return 0;
}
```

