[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1015)
省选前的信心题……

非常简单，并查集+离线处理即可
```
#include<cstdio>
const int maxn=400005,maxe=400005;
int n,e,q,que[maxn],fa[maxn],total,stk[maxn];
int tot,son[maxe],lnk[maxn],nxt[maxe];
bool vis[maxn];
void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
inline int red(){
	int tot=0,f=1;char ch=getchar();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
	while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
	return tot*f;
}
int getfa(int x){
	if (fa[x]==x) return x;
	return fa[x]=getfa(fa[x]);
}
void merge(int x,int y){
	int fx=getfa(x),fy=getfa(y);
	if (fx==fy) return;
	fa[fx]=fy;total--;
}
int main(){
	n=red(),e=red();
	for (int i=1;i<=n;i++) fa[i]=i;
	for (int i=1,x,y;i<=e;i++) x=red()+1,y=red()+1,add(x,y),add(y,x);
	q=red();total=n;
	for (int i=1;i<=q;i++) que[i]=red()+1,vis[que[i]]=1;
	for (int i=1;i<=n;i++)
	 if (!vis[i])
	  for (int j=lnk[i];j;j=nxt[j])
	   if (!vis[son[j]]) merge(i,son[j]);
	for (int i=q;i;i--){
		stk[++stk[0]]=total-i;
		vis[que[i]]=0;
		for (int j=lnk[que[i]];j;j=nxt[j])
		 if (!vis[son[j]]) merge(que[i],son[j]);
	}
	printf("%d\n",total);
	while (stk[0]) printf("%d\n",stk[stk[0]--]);
	return 0;
}
```