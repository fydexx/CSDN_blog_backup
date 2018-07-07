# 【单调栈】Codeforces 601B Lipshitz Sequence

[题面在这里](http://codeforces.com/problemset/problem/601/B)



单调栈裸题……



可以发现一个很显然的结论：$L(h)$只会从相邻两个位置得到

具体证明可以把$L(h)$看作斜率

  

然后每个子序列的权值就是$Max\{ |a_i-a_{i-1}|\}$

把$a_i-a_{i-1}$看成$d_i$，用一个单调栈维护就好了

因为询问只有100，所以每次询问可以直接$O(n)$搞。



示例程序：

```C++
#include<cstdio>
#define _abs(x) ((x)<0?-(x):(x))
#define LL long long
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

const int maxn=100005;
int n,N,q,a[maxn],stk[maxn];
LL d[maxn];
int main(){
	n=red();q=red();
	for (int i=1;i<=n;i++) a[i]=red();
	while (q--){
		int l=red(),r=red();N=0;
		for (int i=l+1;i<=r;i++)
		 d[++N]=_abs(a[i]-a[i-1]);
		LL now=0,ans=0;
		int til=0;
		for (int i=1;i<=N;i++){
			while (til>0&&d[stk[til]]<=d[i])
			 now-=(stk[til]-stk[til-1])*d[stk[til]],til--;
			now+=(i-stk[til])*d[i];stk[++til]=i;
			ans+=now;
		}
		printf("%lld\n",ans);
	}
	return 0;
} 
```

