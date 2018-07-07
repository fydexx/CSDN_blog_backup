[题面在这里](http://www.51nod.com/onlineJudge/questionCode.html#!problemId=1277)

首先要明确一个性质：字符串border的border是原串的border
所以一个前缀是多少个前缀的border，它的出现次数就是多少

所以在next树上，子树的大小就是它的出现次数
统计一下就好了

示例程序：

```
#include<cstdio>
#include<cstring>
#include<algorithm>
#define LL long long
using namespace std;

const int maxn=100005;
int n,fail[maxn],num[maxn];
int tot,lnk[maxn],nxt[maxn],son[maxn];
inline void add(int x,int y){
	son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
char s[maxn];
void dfs(int x){
	num[x]=1;
	for (int j=lnk[x];j;j=nxt[j])
	 dfs(son[j]),num[x]+=num[son[j]];
}
int main(){
	scanf("%s",s+1);
	n=strlen(s+1);
	fail[1]=0;add(0,1);
	for (int i=2,j=0;i<=n;i++){
		while (j>0&&s[j+1]!=s[i]) j=fail[j];
		if (s[j+1]==s[i]) j++;
		fail[i]=j;add(j,i);
	}
	dfs(0);LL ans=0;
	for (int i=1;i<=n;i++)
	 ans=max(ans,(LL)num[i]*i);
	printf("%lld",ans);
	return 0;
}
```