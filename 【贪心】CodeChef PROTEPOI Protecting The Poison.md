# 【贪心】CodeChef PROTEPOI Protecting The Poison

[题面在这里](https://www.codechef.com/problems/PROTEPOI)

其实很显然，

把所有蛇都投影到垂直的两个方向

对两个方向分别进行扫描线就可以了



示例程序：

```
#include<cstdio>
#include<algorithm>
using namespace std;

const int maxn=100005,INF=0x3f3f3f3f;
int tst,n,k,m,L,R,hx[maxn],hy[maxn],tx[maxn],ty[maxn],ans=0;
struct data{
	int s,t;
	bool operator<(const data&b)const{
		return s<b.s;
	}
}a[maxn];
void work(){
	int lst=L;
	for (int i=1;i<=n&&lst<=R;){
		if (lst<a[i].s) {ans-=INF;return;}
		int far=-1;
		while (i<=n&&a[i].s<=lst) far=max(far,a[i++].t);
		lst=far+1;ans++;
	}
	if (lst<=R) ans-=INF;
}
int main(){
	scanf("%d",&tst);
	while (tst--){
		scanf("%d%d%d",&n,&k,&m);
		L=(n-k)/2+1;R=L+k-1;
		for (int i=1;i<=m;i++){
			scanf("%d%d%d%d",&hx[i],&hy[i],&tx[i],&ty[i]);
			if (hx[i]==tx[i]){
				if (hy[i]>ty[i]) swap(hy[i],ty[i]);
				a[i].s=hy[i];a[i].t=ty[i];
			}else a[i].s=a[i].t=hy[i];
		}
		sort(a+1,a+1+m);
		ans=0;work();
		for (int i=1;i<=m;i++)
		 if (hy[i]==ty[i]){
			if (hx[i]>tx[i]) swap(hx[i],tx[i]);
			a[i].s=hx[i];a[i].t=tx[i];
		 }else a[i].s=a[i].t=hx[i];
		sort(a+1,a+1+m);
		work();
		printf("%d\n",ans<0?-1:ans);
	}
	return 0;
}
```

