# 【二维树状数组】BZOJ1452 [JSOI2009]Count

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1452)



二维树状数组裸题……

不解释了，直接上代码



示例程序：

```C++
#include<cstdio>
#define lowbit(x) ((x)&-(x))
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

const int maxn=305,maxc=105;
int n,m,q,a[maxn][maxn],tre[maxc][maxn][maxn];
void insert(int c,int x,int y,int w){
	for (int i=x;i<=n;i+=lowbit(i))
	 for (int j=y;j<=m;j+=lowbit(j))
	  tre[c][i][j]+=w;
}
int qry(int c,int x,int y){
	int res=0;
	for (int i=x;i;i-=lowbit(i))
	 for (int j=y;j;j-=lowbit(j))
	  res+=tre[c][i][j];
	return res;
}
int main(){
	n=red(),m=red();
	for (int i=1;i<=n;i++)
	 for (int j=1;j<=m;j++)
	  a[i][j]=red(),insert(a[i][j],i,j,1);
	q=red();
	while (q--)
	 if (red()==1){
	 	int x=red(),y=red(),c=red();
	 	insert(a[x][y],x,y,-1);insert(c,x,y,1);
	 	a[x][y]=c;
	 }else{
	 	int x=red(),xx=red(),y=red(),yy=red(),c=red();
	 	printf("%d\n",qry(c,xx,yy)-qry(c,x-1,yy)-qry(c,xx,y-1)+qry(c,x-1,y-1));
	 }
	 return 0;
}
```

