# 【KMP,next树】BZOJ3670 [Noi2014]动物园

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3670)



先KMP建出next树，

那么num[i]就是i的祖先中最大的编号小于等于i/2的深度

用一个指针维护一下就可以得到了



示例程序：

```C++
#include<cstdio>
#include<cstring>
#define cl(x,y) memset(x,y,sizeof(x))
#define LL long long

const int maxn=1000005,tt=1000000007;
int tst,n,fal[maxn],p,to[maxn],dep[maxn];
char s[maxn];
int tot,nxt[maxn],son[maxn],lnk[maxn];
void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
LL ans;
void dfs(int x,int fa){
	dep[x]=dep[fa]+1;
	while (to[p]*2<=x) p=to[p];
	(ans*=dep[p]+1)%=tt;
	for (int j=lnk[x],t=p;j;j=nxt[j]){
		to[x]=son[j];dfs(son[j],x);
		p=t;
	}
}
int main(){
	scanf("%d",&tst);
	while (tst--){
		scanf("%s",s+1);
		n=strlen(s+1);
		cl(lnk,0);tot=0;
		fal[1]=0;add(0,1);
		for (int i=2,j=0;i<=n;i++){
			while (j&&s[j+1]!=s[i]) j=fal[j];
			if (s[j+1]==s[i]) j++;
			fal[i]=j;add(j,i);
		}
		p=0;to[0]=1;ans=1;dep[0]=-1;
		dfs(0,0);
		printf("%lld\n",ans);
	}
}
```

