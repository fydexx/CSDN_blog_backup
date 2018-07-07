今天抽空学习了下这个简单的算法……

背景
--
Manacher用于求最长回文子串问题
是最简单也是最为常用的算法

首先，我们需要考虑回文串长度的奇偶
会发现长度为奇时，回文串有一个对称点
长度为偶时则不然
为了方便解题，我们需要对原字符串进行预处理：
```
orzLynstery
#o#r#z#L#y#n#s#t#e#r#y#
```
可以发现，每个空隙中填充'#'后，回文子串长度一定为奇

做法
--
定义$f[i]$表示 以i为对称点的 最大回文子串的半长（包括i）
从左往右依次处理出f[]……
记maxR为回文子串右边界的最大值，pos为maxR对应回文子串的对称点
当$i<maxR$时：
![这里写图片描述](http://img.blog.csdn.net/20170613145617792?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
由于区间[maxR',maxR]是关于pos对称的
所以f[i]至少能达到f[j]的大小
但是如果j对应的回文子串越出了maxR'，f[i]就只能确定至少为maxR-i
![这里写图片描述](http://img.blog.csdn.net/20170613150350519?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
如果更惨，连i都大于maxR了，f[i]只能为1
所以：

```
f[i]=i<maxR?min(f[pos*2-i],maxR-i+1):1;
```

当然，以上只是在能确定的范围内尽量使f[i]更大
接下来还是需要枚举f[i]的
（暴力枚举）注意一下边界：

```
while (i-f[i]>0&&i+f[i]<=n&&s[i-f[i]]==s[i+f[i]]) f[i]++;
```

这里不得不讲一下答案的统计：
因为所得到的f[i]是基于处理过的字符串
所以真正的答案应该是
$回文子串长度=Max\{f[i]-1\}$

例题
--
[hihoCoder1032](http://hihocoder.com/problemset/problem/1032)

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