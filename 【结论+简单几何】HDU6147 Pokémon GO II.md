# 【结论+简单几何】HDU6147 Pokémon GO II

[题面在这里](http://acm.hdu.edu.cn/showproblem.php?pid=6147)

不难发现，第一次路径相交时，这两条线段出现的时间差不超过6

然后就很好搞了

注意判断线段相交有很多小细节

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define LL long long
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

const int p[4][2]={{-1,0},{0,1},{1,0},{0,-1}};
int tst,n;
struct seg{
	LL x,y,xx,yy;
	seg () {}
	seg (LL _x,LL _y,LL _xx,LL _yy):x(_x),y(_y),xx(_xx),yy(_yy) {}
	void maintain(){
		if (x==xx){
			if (y>yy) swap(y,yy);
		}else if (x>xx) swap(x,xx);
	}
	bool check(const seg&b){
		if (x==xx&&y==yy) return 0;
		if (x==xx){
			if (b.y==b.yy) return y<=b.y&&b.y<=yy && b.x<=x&&x<=b.xx;
			 else return x==b.x&&!(b.yy<y||yy<b.y);
		}else{
			if (b.x==b.xx) return x<=b.x&&b.x<=xx && b.y<=y&&y<=b.yy;
			 else return y==b.y&&!(b.xx<x||xx<b.x);
		}
	}
}a[10];
int main(){
	tst=red();
	while (tst--){
		n=red();memset(a,0,sizeof(a));
		int til=0,i=0;LL x=0,y=0;
		bool suc=1;
		for (int j=1;j<=n;j++){
			LL l=red();
			a[til]=seg(x,y,x+p[i][0]*l,y+p[i][1]*l);
			a[til].maintain();
			x+=p[i][0]*l;y+=p[i][1]*l;i=(i+1)%4;
			for (int k=0;k<6;k++)
			 if (k!=til&&k!=(til+5)%6&&a[k].check(a[til])) {printf("%d\n",j);suc=0;break;}
			if (!suc) while (++j<=n) red();
			til=(til+1)%6;
		}
		if (suc) printf("Catch you\n");
	}
	return 0;
}
```