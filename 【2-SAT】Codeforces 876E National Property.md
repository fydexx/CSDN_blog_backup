# 【2-SAT】Codeforces 876E National Property

[题面在这里](http://codeforces.com/problemset/problem/876/E)



其实就是2-SAT……

对于每个字母，考虑是否改掉

那么只需要相邻两个满足字典序，所有的单词就都满足字典序了

然后应该很好处理吧……



nm打反狂WA 3发……



示例程序：

```C++
#include<cstdio>
#include<vector>
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

const int maxn=200005,maxe=400005;
int N,n,m,l[maxn];
vector<int> s[maxn];
int son[maxe],lnk[maxn],nxt[maxe],tot;
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
int dfn[maxn],low[maxn],stk[maxn],to[maxn],Tim,scc[maxn];
bool instk[maxn];
void tarjan(int x){
    stk[++stk[0]]=x;instk[x]=1;
    dfn[x]=low[x]=++Tim;
    for (int j=lnk[x];j;j=nxt[j])
     if (!dfn[son[j]]) tarjan(son[j]),low[x]=min(low[x],low[son[j]]);else
     if (instk[son[j]]) low[x]=min(low[x],dfn[son[j]]);
    if (low[x]==dfn[x]){
        scc[0]++;
        while (stk[stk[0]+1]!=x)
         instk[stk[stk[0]]]=0,scc[stk[stk[0]--]]=scc[0];
    }
}
int main(){
	n=red(),m=red();
	for (int i=1;i<=n;i++){
		l[i]=red();s[i].push_back(0);
		for (int j=1;j<=l[i];j++) s[i].push_back(red());
	}
	for (int i=1;i<n;i++){
		bool fir=1;
		for (int j=1;j<=l[i]&&j<=l[i+1];j++)
		 if (fir&&s[i][j]!=s[i+1][j]){
		 	fir=0;int a=s[i][j],b=s[i+1][j];
		 	if (a<b)
		 	 add(a<<1,b<<1),add(b<<1|1,a<<1|1);
		 	else
		 	 add(a<<1,a<<1|1),add(b<<1|1,b<<1);
		 }
		if (fir&&l[i+1]<l[i]) return printf("No"),0; 
	}
	N=m<<1|1;
	for (int i=2;i<=N;i++)
	 if (!dfn[i]) tarjan(i);
	int tot=0;
	for (int i=1;i<=m;i++)
	 if (scc[i<<1]==scc[i<<1|1]) return printf("No"),0;
	  else if (scc[i<<1|1]<scc[i<<1]) tot++;
	printf("Yes\n%d\n",tot);
	for (int i=1;i<=m;i++)
	 if (scc[i<<1|1]<scc[i<<1]) printf("%d ",i);
	return 0;
}
```

