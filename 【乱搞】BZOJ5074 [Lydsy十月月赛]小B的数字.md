# 【乱搞】BZOJ5074 [Lydsy十月月赛]小B的数字

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=5074)



可以感觉到当N很大时，基本都是输出NO的

如何证明呢？

设$b_i=2^{c_i},\sum c_i=S$

则有：

$\forall i \text{满足}S\le a_i\cdot c_i,\text{即} \frac S {a_i}\le c_i$

考虑这个式子：

$\frac S n\le c_i$这个显然不能对所有的i成立（除非所有ci都相等）

那么如果$n>a_i$，$\frac S {a_i}\le c_i$就一定不对所有的i成立

所以$n\le a_i\le 10$才有可能输出YES

然后就可以暴枚$S$，XJB验证就好了



示例程序：

```C++
#include<cstdio>
#include<cmath>
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

const int maxn=20;
int tst,n,a[maxn];
bool check(int s){
	int sum=0;
	for (int i=1;i<=n;i++)
	 sum+=ceil((double)s/a[i]);
	return sum<=s;
}
int main(){
	tst=red();
	while (tst--){
		n=red();
		if (n>10) {while (n--) red();printf("NO\n");continue;}
		for (int i=1;i<=n;i++) a[i]=red();
		bool suc=0;
		for (int i=1;i<=500000;i++)
		 if (check(i)) {suc=1;break;}
		if (suc) printf("YES\n");else printf("NO\n");
	}
	return 0;
}
```

