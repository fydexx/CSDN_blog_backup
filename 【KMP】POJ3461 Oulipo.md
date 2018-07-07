[题面在这里](http://poj.org/problem?id=3461)

没什么好说的……直接KMP就好了

示例程序：

```
#include<cstdio>
#include<cstring>

const int maxn=1000005;
int tst,n,m,nxt[maxn];
char a[maxn],b[maxn];
int main(){
	scanf("%d",&tst);
	while (tst--){
		scanf("%s%s",b+1,a+1);
		n=strlen(a+1);m=strlen(b+1);
		nxt[1]=0;
		for (int i=2,j=0;i<=m;i++){
			while (j>0&&b[j+1]!=b[i]) j=nxt[j];
			if (b[j+1]==b[i]) j++;
			nxt[i]=j;
		}
		int ans=0;
		for (int i=1,j=0;i<=n;i++){
			while (j>0&&b[j+1]!=a[i]) j=nxt[j];
			if (b[j+1]==a[i]) j++;
			if (j==m) ans++,j=nxt[j];
		}
		printf("%d\n",ans);
	}
	return 0;
}
```