# 【LCT】BZOJ2049 [Sdoi2008]Cave 洞穴勘测

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2049)



LCT模板题，没什么好说的

判断是否联通只需要判断根是否相同即可

暴力往上找根是可行的，因为树的均摊深度是$logn$



示例程序：

```C++
#include<cstdio>
#include<algorithm>
using namespace std;
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline char fstchar(){
	char ch=nc();while (ch<'A'||'Z'<ch) ch=nc();
	return ch;
}
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res*f;
}

const int maxn=10005;
int n,q;
int s[maxn][2],fa[maxn];
bool flp[maxn];
#define isroot(x) (s[fa[x]][0]!=x&&s[fa[x]][1]!=x)
inline void addflip(int x) {swap(s[x][0],s[x][1]),flp[x]^=1;}
inline void pushdown(int x) {if (flp[x]) flp[x]^=1,addflip(s[x][0]),addflip(s[x][1]);}
inline void rotate(int x){
	int f=fa[x],g=fa[f],d=s[f][0]==x;
	if (!isroot(f)) s[g][s[g][1]==f]=x; fa[x]=g;
	s[f][d^1]=s[x][d]; fa[s[x][d]]=f;
	s[x][d]=f; fa[f]=x;
}
int stk[maxn],top;
inline void splay(int x){
	top=0;stk[++top]=x;
	for (int i=x;!isroot(i);i=fa[i]) stk[++top]=fa[i];
	while (top) pushdown(stk[top--]);
	while (!isroot(x)){
		int f=fa[x],g=fa[f],d=s[f][0]==x,dd=s[g][0]==f;
		if (!isroot(f))
		 if (d==dd) rotate(f),rotate(x);
		 else rotate(x),rotate(x);
		else rotate(x);
	}
}
inline void access(int x) {for (int t=0;x;t=x,x=fa[x]) splay(x),s[x][1]=t;}
inline void mr(int x) {access(x);splay(x);addflip(x);}
inline int sfa(int x) {while (fa[x]) x=fa[x];return x;}
inline void join(int x,int y) {mr(x);fa[x]=y;}
inline void cut(int x,int y) {mr(x);access(y);splay(y);fa[x]=s[y][0]=0;}
int main(){
	n=red(),q=red();
	while (q--){
		char c=fstchar();int x=red(),y=red();
		if (c=='Q') printf(sfa(x)==sfa(y)?"Yes\n":"No\n");else
		if (c=='C') join(x,y);else cut(x,y);
	}
	return 0;
}
```

