# 【分治计数】BZOJ3745 [Coci2015]Norma

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3745)



分治计数的经典题目……

对$mid$右边的每个位置维护以下量：

$i-mid$的前缀和

前缀$max$和$min$

前缀$max$和$min$的前缀和$mx$和$mn$

$mx\cdot mn$的前缀和

$mx\cdot mn  (i-mid)$的前缀和

$mx(i-mid)$和$mn(i-mid)$的前缀和



然后JXB讨论一下就好了



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

const int maxn=500005,INF=0x3f3f3f3f,tt=1000000000;
int n,a[maxn];
LL ans,s[maxn],smx[maxn],smn[maxn],ss[maxn],ssl[maxn],smxl[maxn],smnl[maxn],mx[maxn],mn[maxn];
void devide(int L,int R){
	if (L==R) {(ans+=(LL)a[L]*a[R]%tt)%=tt;return;}
	int mid=L+R>>1;
	devide(L,mid);devide(mid+1,R);
	s[mid]=smx[mid]=smn[mid]=mx[mid]=ss[mid]=ssl[mid]=smxl[mid]=smnl[mid]=0; mn[mid]=INF;
	for (int i=mid+1;i<=R;i++){
		mx[i]=max(mx[i-1],(LL)a[i]); mn[i]=min(mn[i-1],(LL)a[i]);
		smx[i]=(smx[i-1]+mx[i])%tt; smn[i]=(smn[i-1]+mn[i])%tt;
		s[i]=(s[i-1]+i-mid)%tt; ss[i]=(ss[i-1]+mx[i]*mn[i])%tt;
		ssl[i]=(ssl[i-1]+mx[i]*mn[i]%tt*(i-mid))%tt;
		smxl[i]=(smxl[i-1]+mx[i]*(i-mid))%tt; smnl[i]=(smnl[i-1]+mn[i]*(i-mid))%tt;
	}
	LL Max=0,Min=INF;
	for (int i=mid,jx=mid,jm=mid;i>=L;i--){
		Max=max(Max,(LL)a[i]);Min=min(Min,(LL)a[i]);
		while (jx<R&&mx[jx+1]<Max) jx++;
		while (jm<R&&mn[jm+1]>Min) jm++;
		int x=min(jx,jm),y=max(jx,jm);LL ii=mid-i+1;
		(ans+=(s[x]+ii*(x-mid))*Max%tt*Min)%=tt;
		if (jx<=jm)
		 (ans+=(smxl[y]-smxl[x] + ii*(smx[y]-smx[x]))%tt*Min)%=tt;
		else
		 (ans+=(smnl[y]-smnl[x] + ii*(smn[y]-smn[x]))%tt*Max)%=tt;
		(ans+=ssl[R]-ssl[y] + ii*(ss[R]-ss[y]))%=tt;
	}
}
int main(){
	n=red();
	for (int i=1;i<=n;i++) a[i]=red();
	devide(1,n);
	printf("%lld",(ans+tt)%tt);
	return 0;
}
```

