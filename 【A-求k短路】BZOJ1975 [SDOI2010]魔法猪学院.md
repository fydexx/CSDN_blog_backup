题面在这里

A*求k短路的经典题目，关于A*可以[看这里](http://blog.csdn.net/linkfqy/article/details/72762954)

附上代码：

```
#include<cstdio>
#include<queue>
#include<cstring>
using namespace std;
const int maxn=5005,maxe=200005;
int n,e,que[maxn],S,T,ans=0;
bool vis[maxn];
double m,f[maxn];
struct edge{
	int son[maxe],nxt[maxe],lnk[maxn],tot;
	double w[maxe];
	void add(int x,int y,double wi){
		son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;w[tot]=wi;
	}
}a,b;
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
void spfa(){
	memset(f,127,sizeof(f));
	int hed=0,til=1;
	f[T]=0;que[1]=T;
	while (hed!=til){
		int x=que[hed=(hed+1)%maxn];
		vis[x]=0;
		for (int j=b.lnk[x];j;j=b.nxt[j])
		 if (f[b.son[j]]>f[x]+b.w[j]){
		 	f[b.son[j]]=f[x]+b.w[j];
		 	if (!vis[b.son[j]])
		 	 que[til=(til+1)%maxn]=b.son[j],
		 	 vis[b.son[j]]=1;
		 }
	}
}
struct data{
	int id;double w;
	data(int x,double y):id(x),w(y) {}
	bool operator<(const data&b)const{
		return w+f[id]>b.w+f[b.id];
	}
};
priority_queue<data> Q;
void Astar(){
	Q.push(data(S,0));
	while (!Q.empty()){
		data k=Q.top();Q.pop();
		if (k.id==T)
		 if (k.w<=m) m-=k.w,ans++;else return;
		for (int j=a.lnk[k.id];j;j=a.nxt[j])
		 Q.push(data(a.son[j],k.w+a.w[j]));
	}
}
int main(){
	n=red(),e=red();scanf("%lf",&m);S=1;T=n;
	for (int i=1;i<=e;i++){
		int x=red(),y=red();double s;
		scanf("%lf",&s);a.add(x,y,s);b.add(y,x,s);
	}
	spfa();
	Astar();
	printf("%d",ans);
	return 0;
}
```