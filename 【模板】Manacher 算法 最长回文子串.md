参考博客：[Manacher算法——最长回文子串](http://blog.csdn.net/linkfqy/article/details/73181032)
Manacher算法的模板，写得比较简洁
作为一个模板，还是不错的

```
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
const int maxn=2000005;
int tst,f[maxn];
char a[maxn],s[maxn];
int Manacher(){
	int n=0,res=0,maxR=0,pos=0;
	for (int i=0;a[i];i++) s[++n]='#',s[++n]=a[i];s[++n]='#';
	for (int i=1;i<=n;i++){
		f[i]=i<maxR?min(f[pos*2-i],maxR-i+1):1;
		while (i-f[i]>0&&i+f[i]<=n&&s[i-f[i]]==s[i+f[i]]) f[i]++;
		if (i+f[i]-1>maxR) maxR=i+f[i]-1,pos=i;
		res=max(res,f[i]-1);
	}
	return res;
}
int main(){
	scanf("%d",&tst);
	for (int i=1;i<=tst;i++) scanf("%s",a),printf("%d\n",Manacher());
	return 0;
}
```