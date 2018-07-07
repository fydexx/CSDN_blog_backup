[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=3687)

~~最喜欢这种乱搞题了~~

因为异或两次就等于没有异或
所以只需要记录不同算术和的奇偶即可
用一个bitset存
假设已经统计好了前面一部分算术和，新加一个x
那么每个数加x或不加，新的bitset就是`(S<<x)^S`

~~果然乱搞题最好了，代码又短~~
示例程序：

```
#include<cstdio>
#include<bitset>
using namespace std;
int n,ans;
bitset<2000005> S;
int main(){
	scanf("%d",&n);
	S.set(0);
	for (int i=1,x;i<=n;i++)
	 scanf("%d",&x),S=(S<<x)^S;
	for (int i=1;i<=2000000;i++)
	 if (S.test(i)) ans^=i;
	return printf("%d",ans),0;
}
```