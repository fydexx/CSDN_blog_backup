#【lucas定理】BZOJ2982 combination

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=2982)



不说了，直接lucas



示例程序：

```C++
#include<cstdio>

const int maxn=10010,MOD=10007;
int tst,fac[maxn],inv[maxn];
void prepare(){
	fac[0]=inv[0]=inv[1]=1;
	for (int i=1;i<=MOD;i++) fac[i]=fac[i-1]*i%MOD;
	for (int i=2;i<=MOD;i++) inv[i]=-(MOD/i)*inv[MOD%i]%MOD;
	for (int i=1;i<=MOD;i++) (inv[i]*=inv[i-1])%=MOD;
}
int C(int x,int y){
	if (x>y) return 0;
	if (x<MOD&&y<MOD)
	 return fac[y]*inv[x]%MOD*inv[y-x]%MOD;
	return C(x/MOD,y/MOD)*C(x%MOD,y%MOD)%MOD;
}
int main(){
	prepare();
	scanf("%d",&tst);
	while (tst--){
		int n,m;scanf("%d%d",&n,&m);
		printf("%d\n",(C(m,n)+MOD)%MOD);
	}
	return 0;
}
```

