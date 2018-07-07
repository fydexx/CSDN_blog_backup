[题面在这里](http://poj.org/problem?id=2406)

可以发现，如果字符串S存在循环节
对其求next，大概是长这样的：
![lalala](http://img.blog.csdn.net/20170801161033197?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
很显然，答案是$\frac N {N-next[N]}$
否则一定不会整除

示例程序：

```
#include<cstdio>
#include<cstring>
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res*f; 
}

const int maxn=1000005;
int n,nxt[maxn];
char s[maxn];
int main(){
	scanf("%s",s+1);
	while (s[1]!='.'||s[2]){
		int n=strlen(s+1);
		nxt[1]=0;
		for (int i=2,j=0;i<=n;i++){
			while (j>0&&s[j+1]!=s[i]) j=nxt[j];
			if (s[j+1]==s[i]) j++;
			nxt[i]=j;
		}
		int ans=n-nxt[n];
		printf("%d\n",n%ans==0?n/ans:1);
		scanf("%s",s+1);
	}
	return 0;
}
```