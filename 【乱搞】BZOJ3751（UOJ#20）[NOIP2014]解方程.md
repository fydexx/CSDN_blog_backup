# 【乱搞】BZOJ3751（UOJ#20）[NOIP2014]解方程

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3751)

[还可以双倍经验](http://uoj.ac/problem/20)



本题其实利用了这样一个显然的事实：
$$
f(x)=0 \Rightarrow f(x)\space mod \space p=0
$$
但是反过来不一定成立

那么多取几个质数，如果对于所有的质数都成立就假装成立好了



示例程序：

```C++
#include<cstdio>
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
inline void reads(char *s){
	char ch=nc();while (ch<'-') ch=nc();
	while (ch>='-') *(s++)=ch,ch=nc();
	*s=0;
}

const int maxn=105,maxs=10005,p[5]={10007,30011,19997,21893,14843};
int n,m,a[5][maxn],que[1000005];
char s[maxs];
bool vis[5][1000005];
void work(int k,int p){
	for (int x=1;x<p;x++){
		int res=0;
		for (int pow=1,i=0;i<=n;i++,pow=pow*x%p) (res+=pow*a[k][i])%=p;
		res=(res+p)%p;
		if (res==0) vis[k][x]=1;
	}
}
inline bool check(int x){
	for (int i=0;i<5;i++)
	 if (!vis[i][x%p[i]]) return 0;
	return 1;
}
int main(){
	n=red(),m=red();
	for (int i=0;i<=n;i++){
		reads(s);
		for (int k=0;k<5;k++){
			int f=1;
			for (int j=0;s[j];j++)
			 if (s[j]=='-') f=-f;else a[k][i]=(a[k][i]*10+s[j]-48)%p[k];
			a[k][i]*=f;
		}
	}
	for (int i=0;i<5;i++) work(i,p[i]);
	int ans=0;
	for (int i=1;i<=m;i++) if (check(i)) que[++ans]=i;
	printf("%d\n",ans);
	for (int i=1;i<=ans;i++) printf("%d\n",que[i]);
	return 0;
}
```

