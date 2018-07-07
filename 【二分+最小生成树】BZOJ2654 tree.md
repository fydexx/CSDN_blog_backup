#【二分+最小生成树】BZOJ2654 tree

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2654)



其实非常简单：

发现恰有need条边很难控制，但是边的优先级很好控制

所以考虑给每个白色边加上某个权值

使得求最小生成树时刚好取了need条边

然后发现这个加上的权值与白色边的条数刚好满足单调

所以直接二分加上的权值就好了



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
	while ('0'<=ch&&ch<='9') res=(res<<3)+(res<<1)+ch-48,ch=nc();
	return res*f;
}

const int maxn=50005,maxe=100005;
int n,e,ned,ans,fa[maxn];
struct edge{
	int x,y,w,t;
	bool operator<(const edge&b)const{
		return w<b.w;
	}
}a[maxe];
int getfa(int x){
	return fa[x]==x?x:fa[x]=getfa(fa[x]);
}
bool check(int de){
	for (int i=1;i<=e;i++)
	 if (!a[i].t) a[i].w+=de;
	sort(a+1,a+1+e);
	for (int i=1;i<=n;i++) fa[i]=i;
	int tot=0,white=0,res=0;
	for (int i=1;i<=e&&tot<n-1;i++){
		if (getfa(a[i].x)==getfa(a[i].y)) continue;
		fa[getfa(a[i].x)]=getfa(a[i].y);
		tot++;if (!a[i].t) white++;
		res+=a[i].w;
	}
	for (int i=1;i<=e;i++)
	 if (!a[i].t) a[i].w-=de;
	if (white>=ned) ans=min(ans,res-white*de);
	return white>=ned;
}
int main(){
	n=red(),e=red(),ned=red();
	for (int i=1;i<=e;i++)
	 a[i].x=red()+1,a[i].y=red()+1,a[i].w=red(),a[i].t=red();
	int L=-100,R=100;ans=0x3f3f3f3f;
	while (L<=R){
		int mid=L+R>>1;
		if (check(mid)) L=mid+1;else R=mid-1;
	}
	printf("%d",ans);
	return 0;
}
```

