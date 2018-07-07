# 【DFS】Codeforces 717E Paint it really, really dark gray

[题面在这里](http://codeforces.com/problemset/problem/717/E)



其实就是用DFS递归处理：

对于子树x，先把它的所有儿子子树处理掉

然后再看x，如果颜色不对，为了不改变下面的颜色，就$x\rightarrow fa \rightarrow x$

如果x是根就比较特殊了，可以$x\rightarrow son\rightarrow x$



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

const int maxn=200005,maxe=400005;
int n;
int tot,son[maxe],lnk[maxn],nxt[maxe];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
bool a[maxn];
void dfs(int x,int fa){
	printf("%d ",x);int tem=0;
	for (int j=lnk[x];j;j=nxt[j])
	 if (son[j]!=fa)
	  a[son[j]]=!a[son[j]],dfs(son[j],x),printf("%d ",x),a[x]=!a[x],tem=son[j];
	if (!a[x]){
		if (x!=1) printf("%d %d ",fa,x),a[fa]=!a[fa];else
		printf("%d %d %d",tem,x,tem);
		a[x]=!a[x];
	}
}
int main(){
	n=red();
	for (int i=1;i<=n;i++) a[i]=(red()==1);
	for (int i=1,x,y;i<n;i++) x=red(),y=red(),add(x,y),add(y,x);
	dfs(1,0);
	return 0;
}
```

