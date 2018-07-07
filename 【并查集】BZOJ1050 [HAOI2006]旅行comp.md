[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1050)

又是一道水题……

可以直接枚举最大边与最小边，
那么位于这之间的边都可以走，
用并查集判断S到T是否联通即可

示例程序：

```
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn=505,maxe=5005;
int n,e,S,T,fa[maxn];
struct edge{
	int x,y,w;
	bool operator<(const edge&b)const{
		return w<b.w;
	}
}a[maxe];
inline int red(){
	int tot=0;char ch=getchar();
	while (ch<'0'||'9'<ch) ch=getchar();
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot;
}
int getfa(int x){
	return fa[x]==x?x:fa[x]=getfa(fa[x]);
}
void merge(int x,int y){
	fa[getfa(x)]=getfa(y);
}
int gcd(int x,int y){
	return y==0?x:gcd(y,x%y);
}
int main(){
	n=red(),e=red();
	for (int i=1;i<=e;i++) a[i].x=red(),a[i].y=red(),a[i].w=red();
	S=red(),T=red();
	sort(a+1,a+1+e);
	int ansx=40000,ansy=1;
	for (int i=e;i>=1;i--){
		for (int j=1;j<=n;j++) fa[j]=j;
		for (int j=i;j>=1;j--){
			merge(a[j].x,a[j].y);
			if (getfa(S)==getfa(T)){
				if (a[i].w*ansy<a[j].w*ansx) ansx=a[i].w,ansy=a[j].w;
				break;
			}
		}
	}
	int t=gcd(ansx,ansy);
	ansx/=t;ansy/=t;
	if (ansx==40000) printf("IMPOSSIBLE");else
	 if (ansy>1) printf("%d/%d",ansx,ansy);else printf("%d",ansx);
	return 0;
}
```