# 【CDQ分治】 BZOJ3262 陌上花开

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3262)



最经典的三维偏序问题

x用CDQ分治，y排序，z树状数组维护



示例程序：

```c++
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
int n,m=2e5,num[maxn],ans[maxn];
struct data{
	int x,y,z,id;
	inline void read() {x=red();y=red();z=red();}
	inline bool operator==(const data&b)const{return x==b.x&&y==b.y&&z==b.z;}
}a[maxn],t[maxn];
inline const bool cmp(const data&a,const data&b){
	return a.x<b.x || a.x==b.x&&a.y<b.y || a.x==b.x&&a.y==b.y&&a.z<b.z;
}
inline const bool cmpy(const data&a,const data&b){
	return a.y<b.y||a.y==b.y&&a.id<b.id;
}
#define lowbit(x) ((x)&-(x))
int BIT[maxn];
inline void ist(int x,int w){
	for (;x<=m;x+=lowbit(x)) BIT[x]+=w;
}
inline int ask(int x){
	int res=0;
	for (;x;x-=lowbit(x)) res+=BIT[x];
	return res;
}
void CDQ(int l,int r){
	if (l==r) return;
	int mid=l+r>>1;
	CDQ(l,mid);CDQ(mid+1,r);
	for (int i=l;i<=r;i++){
		t[i]=a[i];
		if (i<=mid) t[i].id=0;
	}
	sort(t+l,t+r+1,cmpy);
	for (int i=l;i<=r;i++)
	 if (!t[i].id) ist(t[i].z,1);else num[t[i].id]+=ask(t[i].z);
	for (int i=l;i<=r;i++)
	 if (!t[i].id) ist(t[i].z,-1);
}
int main(){
	n=red();m=red();
	for (int i=1;i<=n;i++) a[i].read(),a[i].id=i;
	sort(a+1,a+1+n,cmp);
	for (int i=n-1,t=0;i;i--){
		if (a[i]==a[i+1]) t++;else t=0;
		num[a[i].id]=t;
	}
	CDQ(1,n);
	for (int i=1;i<=n;i++) ans[num[i]]++;
	for (int i=0;i<n;i++) printf("%d\n",ans[i]); 
	return 0;
}
```

