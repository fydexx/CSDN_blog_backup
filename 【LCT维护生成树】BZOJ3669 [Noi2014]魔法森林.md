# 【LCT维护生成树】BZOJ3669 [Noi2014]魔法森林

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3669)



考虑枚举a的最大值

那么只需要让$1\rightarrow n$的最大值最小即可

这样其实就是在做生成树，若当前构成环，则删去环中的最大边

如果1到n联通就更新答案了

具体实现可以按a排序，枚举每条边作为最大边

用LCT维护生成树，其中最大值信息需要保存边的标号

然后因为这道题是边权，所以要将边转化为点处理



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

const int maxn=200005,INF=0x3f3f3f3f;
int n,e,ans=INF;
struct edge{
	int x,y,a,b;
	bool operator<(const edge&b)const {return a<b.a;}
	inline void read() {x=red(),y=red(),a=red(),b=red();}
}a[maxn];
int s[maxn][2],fa[maxn],id[maxn],w[maxn];
bool flp[maxn];
#define isroot(x) (s[fa[x]][0]!=x&&s[fa[x]][1]!=x) 
inline void addflip(int x) {swap(s[x][0],s[x][1]),flp[x]^=1;}
inline void pushup(int x){
	id[x]=x;
	if (w[id[x]]<w[id[s[x][0]]]) id[x]=id[s[x][0]];
	if (w[id[x]]<w[id[s[x][1]]]) id[x]=id[s[x][1]];
}
inline void pushdown(int x) {if (flp[x]) flp[x]^=1,addflip(s[x][0]),addflip(s[x][1]);}
inline void rotate(int x){
	int f=fa[x],g=fa[f],d=s[f][0]==x;
	if (!isroot(f)) s[g][s[g][1]==f]=x; fa[x]=g;
	s[f][d^1]=s[x][d]; fa[s[x][d]]=f;
	s[x][d]=f; fa[f]=x;
	pushup(f); pushup(x);
}
int stk[maxn],top;
inline void splay(int x){
	stk[++top]=x;
	for (int i=x;!isroot(i);i=fa[i]) stk[++top]=fa[i];
	while (top) pushdown(stk[top--]);
	while (!isroot(x)){
		int f=fa[x],g=fa[f],d=s[f][0]==x,dd=s[g][0]==f;
		if (!isroot(f))
		 if (d==dd) rotate(f),rotate(x);
		 else rotate(x),rotate(x);
		else rotate(x);
	}
}
inline void access(int x) {for (int t=0;x;t=x,x=fa[x]) splay(x),s[x][1]=t,pushup(x);}
inline void mr(int x) {access(x);splay(x);addflip(x);}
inline void join(int x,int y) {mr(x);fa[x]=y;}
inline void cut(int x,int y) {mr(x);access(y);splay(y);fa[x]=s[y][0]=0;}
inline int sfa(int x) {while (fa[x]) x=fa[x];return x;}
inline int getmax(int x,int y) {mr(x);access(y);splay(y);return w[id[y]];}
int main(){
	n=red(),e=red();
	for (int i=1;i<=e;i++) a[i].read();
	sort(a+1,a+1+e);
	for (int i=1;i<=e;i++) w[n+i]=a[i].b;
	for (int i=1;i<=e;i++){
		if (sfa(a[i].x)!=sfa(a[i].y)) join(a[i].x,n+i),join(n+i,a[i].y);
		else if (getmax(a[i].x,a[i].y)>a[i].b){
			int ed=id[a[i].y]-n;
			cut(a[ed].x,ed+n);cut(a[ed].y,ed+n);
			join(a[i].x,n+i),join(n+i,a[i].y);
		}
		if (sfa(1)==sfa(n)) ans=min(ans,a[i].a+getmax(1,n));
	}
	printf("%d",ans==INF?-1:ans);
	return 0;
}
```

