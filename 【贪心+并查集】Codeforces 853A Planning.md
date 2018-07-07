# 【贪心+并查集】Codeforces 853A Planning

[题面在这里](http://codeforces.com/problemset/problem/853/A)



这题其实非常水……

只需要按照$(C_i,i)$的优先级顺序对每个航班取能取的最早的时刻

这个贪心很显然吧……



示例程序：

```C++
#include<cstdio>
#include<algorithm>
using namespace std;
typedef long long LL;
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

const int maxn=600005;
int n,k,fa[maxn],t[maxn];
LL ans;
struct data{
	int c,id;
	data () {}
	data (int _c,int _id):c(_c),id(_id) {}
	bool operator<(const data&b)const{
		return c>b.c||c==b.c&&id<b.id;
	}
}a[maxn];
int getfa(int x){
	return fa[x]==x?x:fa[x]=getfa(fa[x]);
}
int main(){
	n=red(),k=red();
	for (int i=1;i<=n;i++) a[i]=data(red(),i);
	sort(a+1,a+1+n);
	for (int i=1;i<=k;i++) fa[i]=k+1;
	for (int i=k+1;i<=k+n;i++) fa[i]=i;
	for (int i=1;i<=n;i++){
		int j=a[i].id,fj=getfa(j);
		fa[fj]=fj+1;ans+=(LL)(fj-j)*a[i].c;t[j]=fj;
	}
	printf("%lld\n",ans);
	for (int i=1;i<=n;i++) printf("%d ",t[i]);
	return 0;
}
```

