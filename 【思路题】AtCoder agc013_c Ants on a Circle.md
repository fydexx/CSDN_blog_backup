# 【思路题】AtCoder agc013_c Ants on a Circle

[题面在这里](http://agc013.contest.atcoder.jp/tasks/agc013_c)



如果是在一条直线上的就非常好做。

直接把蚂蚁看作可以穿透，然后把所有距离排序就好了

因为无论怎么爬，蚂蚁的相对位置是不会变的

那么对于这题，只需要知道原来的第一个蚂蚁到了哪里就好了

其实就是求穿过$(L-1,0)$这条线的蚂蚁的个数

这个就很好求了，直接搞



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

const int maxn=100005;
int n,L,T,a[maxn],num;
int main(){
	n=red(),L=red(),T=red();
	for (int i=0;i<n;i++){
		int x=red(),f=red();
		if (f==1){
			(num+=(x+T)/L)%=n;
			a[i]=(x+T)%L;
		}else{
			(num-=(L-x-1+T)/L)%=n;
			a[i]=((x-T)%L+L)%L;
		}
	}
	sort(a,a+n);
	for (int i=0;i<n;i++) printf("%d\n",a[(i+num+n)%n]);
	return 0;
}
```

