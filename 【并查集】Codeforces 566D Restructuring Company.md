# 【并查集】Codeforces 566D Restructuring Company

[题面在这里](http://codeforces.com/contest/566/problem/D)



对于本题，只需要再维护一个并查集表示i所在联通块的最右位置

因为相邻两个元素之间的关系至多被处理1次

所以均摊是$O(q\cdot \alpha (n))$



示例程序：

```C++
#include<cstdio>
#define pc putchar
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
int n,q,fa[maxn],nxt[maxn];
int getfa(int x){
	return fa[x]==x?x:fa[x]=getfa(fa[x]);
}
int getnxt(int x){
	return nxt[x]==x?x:nxt[x]=getnxt(nxt[x]);
}
int main(){
	n=red(),q=red();
	for (int i=1;i<=n;i++) fa[i]=nxt[i]=i;
	while (q--){
		int c=red(),x=red(),y=red();
		if (c==1) fa[getfa(x)]=getfa(y);
		 else
		if (c==2){
			for (int j=getnxt(x);j<y;j=getnxt(j+1))
			 fa[getfa(j)]=getfa(y),nxt[getnxt(j)]=getnxt(j+1);
		}else if (getfa(x)==getfa(y)) pc('Y'),pc('E'),pc('S'),pc('\n');else pc('N'),pc('O'),pc('\n');
	}
	return 0;
}
```

