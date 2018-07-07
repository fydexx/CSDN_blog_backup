#【整体二分+树状数组区间加区间和】BZOJ3110 [Zjoi2013]K大数查询

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3110)



同样是整体二分的经典应用：K大数

但是有修改操作。

其实也差不多。



对于$divide(L,R,S)$，将S中所有操作按顺序排列

由于查询第K大的，所以只需要知道有多少数比答案大即可

1. 如果操作是1：如果$c\le mid$说明只可能对答案在$[L,mid]$的查询有贡献，放入$S_1$，否则放入$S_2$

   另外，把放入$S_2$中的操作加到树状数组里面

2. 如果操作是2：考虑$S_2$的所有更新操作对答案属于$[L,mid]$的询问必然有贡献

   只需要到树状数组里面查询一下就可以知道应该加入哪个集合了

树状数组需要支持区间加、区间求和，稍微搞一搞就好了



示例程序：

```C++
#include<cstdio>
#include<algorithm>
#define lowbit(x) ((x)&-(x))
using namespace std;
typedef long long ll;
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

const int maxn=50005,maxm=100005;
int n,m,q;
struct data{
	int t,l,r,c;
}Q[maxn];
inline void add(ll *BIT,int x,int w){
	for (int i=x;i<=m;i+=lowbit(i)) BIT[i]+=w;
}
inline ll qry(ll *BIT,int x){
	int res=0;
	for (int i=x;i;i-=lowbit(i)) res+=BIT[i];
	return res;
}
ll c[maxm],cc[maxm];
inline void insert(int l,int r,int w){
	add(c,l,w);add(c,r+1,-w);
	add(cc,l,w*(l-1));add(cc,r+1,-w*r);
}
inline ll query(int l,int r){
	return qry(c,r)*r-qry(cc,r)-(qry(c,l-1)*(l-1)-qry(cc,l-1));
}
int ans[maxn],tem[maxn],id[maxn];
void divide(int L,int R,int l,int r){
	if (l>r) return;
	if (L==R){
		for (int i=l;i<=r;i++)
		 if (Q[id[i]].t==2) ans[id[i]]=L;
		return;
	}
	int mid=L+R>>1,pl=l-1,pr=r+1;
	for (int i=l;i<=r;i++)
	 if (Q[id[i]].t==1){
	 	if (Q[id[i]].c<=mid) tem[++pl]=id[i];
	 	 else tem[--pr]=id[i],insert(Q[id[i]].l,Q[id[i]].r,1);
	 }else{
	 	ll w=query(Q[id[i]].l,Q[id[i]].r);
	 	if (w>=Q[id[i]].c) tem[--pr]=id[i];
	 	 else tem[++pl]=id[i],Q[id[i]].c-=w;
	 }
	for (int i=l;i<=r;i++)
	 if (Q[id[i]].t==1&&Q[id[i]].c>mid) insert(Q[id[i]].l,Q[id[i]].r,-1);
	for (int i=l;i<=pl;i++) id[i]=tem[i];
	for (int i=pr;i<=r;i++) id[i]=tem[r+pr-i];
	divide(L,mid,l,pl);divide(mid+1,R,pr,r);
}
int main(){
	n=red(),q=red();m=n*2+2;
	int _max=0,_min=1e9;
	for (int i=1;i<=q;i++){
		Q[i].t=red();Q[i].l=red(),Q[i].r=red(),Q[i].c=red();
		id[i]=i;
		if (Q[i].t==1) _max=max(_max,Q[i].c),_min=min(_min,Q[i].c);
	}
	divide(_min,_max,1,q);
	for (int i=1;i<=q;i++)
	 if (Q[i].t==2) printf("%d\n",ans[i]);
	return 0;
}
```

