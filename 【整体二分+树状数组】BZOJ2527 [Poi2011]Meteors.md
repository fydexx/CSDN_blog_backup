# 【整体二分+树状数组】BZOJ2527 [Poi2011]Meteors

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2527)



整体二分裸题……



没什么好说的，对流星雨二分，每个国家的轨道用vector存一下就好了

需要注意一下边界可能爆long long

中间break掉就好了



示例程序：

```C++
#include<cstdio>
#include<vector>
#define lowbit(x) ((x)&-(x))
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

const int maxn=300005;
int n,m,q,p[maxn],s[maxn],t[maxn],a[maxn];
vector<int> orbit[maxn];
LL BIT[maxn];
void add(int x,int w){
	for (int i=x;i<=m;i+=lowbit(i)) BIT[i]+=w;
}
LL qry(int x){
	LL res=0;
	for (int i=x;i;i-=lowbit(i)) res+=BIT[i];
	return res;
}
int id[maxn],tem[maxn],ans[maxn];
void divide(int L,int R,int l,int r){
	if (l>r) return;
	if (L==R){
		for (int i=l;i<=r;i++) ans[id[i]]=L;
		return;
	}
	int mid=L+R>>1,pl=l-1,pr=r+1;
	for (int i=L;i<=mid;i++)
	 if (s[i]<=t[i]) add(s[i],a[i]),add(t[i]+1,-a[i]);else
	  add(s[i],a[i]),add(1,a[i]),add(t[i]+1,-a[i]);
	for (int i=l;i<=r;i++){
		LL res=0;
		for (vector<int>::iterator it=orbit[id[i]].begin();it!=orbit[id[i]].end();it++){
			res+=qry(*it);
			if (res>=p[id[i]]) break;
		}
		if (res>=p[id[i]]) tem[++pl]=id[i];else tem[--pr]=id[i],p[id[i]]-=res;
	}
	for (int i=L;i<=mid;i++)
	 if (s[i]<=t[i]) add(s[i],-a[i]),add(t[i]+1,a[i]);else
	  add(s[i],-a[i]),add(1,-a[i]),add(t[i]+1,a[i]);
	for (int i=l;i<=r;i++) id[i]=tem[i];
	divide(L,mid,l,pl);divide(mid+1,R,pr,r);
}
int main(){
	n=red(),m=red();
	for (int i=1;i<=m;i++) orbit[red()].push_back(i);
	for (int i=1;i<=n;i++) p[i]=red(),id[i]=i;
	q=red();
	for (int i=1;i<=q;i++) s[i]=red(),t[i]=red(),a[i]=red();
	s[q+1]=1;t[q+1]=m;a[q+1]=0x3f3f3f3f;
	divide(1,q+1,1,n);
	for (int i=1;i<=n;i++)
	 if (ans[i]>q) printf("NIE\n");else printf("%d\n",ans[i]);
	return 0;
}
```

