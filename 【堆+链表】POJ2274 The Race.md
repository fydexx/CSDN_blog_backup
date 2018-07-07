[题面在这里](http://poj.org/problem?id=2274)

这道题想法很简单，但是实现比较复杂……
参考隔壁神犇[zzk的题解](http://blog.csdn.net/zzkksunboy/article/details/55005485)。
昨天写好代码后，一WA到底，查了近一个小时……各位同学写的时候小心。


第一问很简单，典型的逆序数（由于v很小，可以用数组代替树状数组）。
至于第二问，要求按顺序输出前10000个“超越事件”，自然就想到用堆来储存“超越事件”（这里使用了二叉堆）
用链表储存赛车的相对位置
每次从堆中取出一个超越事件，注意有可能是非法的（即该超越事件的两个参与者已经不相邻了）
然后就要对这个超越事件进行处理：
原来有四辆赛车：lx,x,y,ry
超越后变成了：lx,y,x,ry
那么lx与y，x与ry都有可能发生新的“超越事件”，判断一下放入堆中即可
代码如下：

```
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn=250005,tt=1000000;
int n,ans,x[maxn],v[maxn],num[105],len;
int lft[maxn],rit[maxn];
struct ha{
	int x,y;
	double Time,Dist;
	bool operator<(const ha&b)const{
		return Time<b.Time||Time==b.Time&&Dist<b.Dist;
	}
}hep[260005];
inline int red(){
	int tot=0;char ch=getchar();
	while (ch<'0'||'9'<ch) ch=getchar();
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot;
}
void psh(ha x){
	hep[++len]=x;int son=len;
	while (son!=1&&hep[son]<hep[son>>1]) swap(hep[son>>1],hep[son]),son>>=1;
}
void pop(){
	hep[1]=hep[len--];int fa=1,son;
	while ((fa<<1)<=len){
		if ((fa<<1|1)>len||hep[fa<<1]<hep[fa<<1|1]) son=fa<<1;else son=fa<<1|1;
		if (hep[son]<hep[fa]) swap(hep[fa],hep[son]),fa=son;else break;
	}
}
double gettime(int i,int j){
	return (double)(x[j]-x[i])/(v[i]-v[j]);
}
double getdist(int i,int j){
	return gettime(i,j)*v[i]+x[i];
}
ha getha(int x,int y,double t,double d){
	ha res;
	res.x=x;res.y=y;res.Time=t;res.Dist=d;
	return res;
}
int main(){
	n=red();
	for (int i=1;i<=n;i++){
		x[i]=red(),v[i]=red();num[v[i]]++;
		for (int j=v[i]+1;j<=100;j++) ans=(ans+num[j])%tt;
		lft[i]=i-1;rit[i]=i+1;
		if (i>1&&v[i-1]>v[i]) psh(getha(i-1,i,gettime(i-1,i),getdist(i-1,i)));
	}v[0]=-1;v[n+1]=200;
	printf("%d\n",ans);
	for (int o=1;len&&o<=10000;o++){
		while (len&&rit[hep[1].x]!=hep[1].y) pop();
		if (!len) break;
		ha now=hep[1];pop();
		printf("%d %d\n",now.x,now.y);
		int lx=lft[now.x],ry=rit[now.y];
		rit[lx]=now.y;lft[ry]=now.x;
		lft[now.y]=lx;rit[now.y]=now.x;
		rit[now.x]=ry;lft[now.x]=now.y;
		if (v[lx]>v[now.y]) psh(getha(lx,now.y,gettime(lx,now.y),getdist(lx,now.y)));
		if (v[now.x]>v[ry]) psh(getha(now.x,ry,gettime(now.x,ry),getdist(now.x,ry)));
	}
	return 0;
}
```