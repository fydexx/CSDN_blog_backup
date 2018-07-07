# 【LCT】BZOJ2002 [Hnoi2010]Bounce 弹飞绵羊

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2002)



LCT模板题，支持$\text {join,cut}$操作即可



示例程序：

```C++
#include<cstdio>
#include<algorithm>
using namespace std;
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

const int maxn=200005;
int n,q,a[maxn];
int s[maxn][2],fa[maxn],siz[maxn];
bool flp[maxn];
inline void addflip(int x) {swap(s[x][0],s[x][1]);flp[x]^=1;}
inline void pushup(int x) {siz[x]=siz[s[x][0]]+siz[s[x][1]]+1;}
inline void pushdown(int x) {if (flp[x]) flp[x]^=1,addflip(s[x][0]),addflip(s[x][1]);}
inline bool isroot(int x) {return s[fa[x]][0]!=x&&s[fa[x]][1]!=x;}
inline void rotate(int x){
	int f=fa[x],g=fa[f],d=(s[f][0]==x);
	if (!isroot(f)) s[g][s[g][1]==f]=x;
	s[f][d^1]=s[x][d]; fa[s[x][d]]=f;
	s[x][d]=f; fa[f]=x; fa[x]=g;
	pushup(f);pushup(x);
}
int stk[maxn],top;
inline void splay(int x){
	top=0;stk[++top]=x;
	for (int j=x;!isroot(j);j=fa[j]) stk[++top]=fa[j];
	while (top) pushdown(stk[top--]);
	while (!isroot(x)){
		int f=fa[x],g=fa[f],d=s[f][0]==x,dd=s[g][0]==f;
		if (!isroot(f))
		 if (d==dd) rotate(f),rotate(x);else rotate(x),rotate(x);
		else rotate(x);
	}
}
inline void access(int x) {for (int t=0;x;t=x,x=fa[x]) splay(x),s[x][1]=t,pushup(x);}
inline void mr(int x) {access(x);splay(x);addflip(x);}
inline void join(int x,int y) {mr(x);fa[x]=y;}
inline void cut(int x,int y){
	mr(x);access(y);splay(y);
	fa[x]=s[y][0]=0;
}
int main(){
	n=red();
	for (int i=1;i<=n+1;i++) siz[i]=1;
	for (int i=1;i<=n;i++)
	 a[i]=min(red()+i,n+1),join(i,a[i]);
	q=red();
	while (q--)
	 if (red()==1){
	 	int x=red()+1;
	 	mr(n+1);
		 access(x);
		 splay(x);
		printf("%d\n",siz[x]-1);
	 }else{
	 	int x=red()+1,k=min(red()+x,n+1);
	 	cut(x,a[x]);join(x,k);a[x]=k;
	 }
	return 0;
}
```

