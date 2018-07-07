前言
--
话说昨天考试有一道题目，可以用bitset艹过去
可是LZ并不会啊……于是只好打暴力……
结果可想而知……
于是LZ下定决心一定要把bitset学透
然后……就有了这篇blog

bitset定义与初始化
------------
bitset是STL提供的用于记录01串的容器
也就是bitset的每个元素只能为0/1

用bitset之前别忘了：

```
#include<bitset>
```

以下是正确的定义方式：

```
bitset<16> a;  //第0~15位都是0
bitset<6> b(string("010010"));  //用字符串初始化b
bitset<32> c(0x80000000);  //第0位是1，其他都是0
```
注意bitset声明后长度不可改变
和数组一样，bitset从0开始编号

bitset的操作
---------
以下是bitset常用操作：

```
a.any()  //a中是否含1
a.none()  //a是否全为0
a.count()  //a中有几个1
a.[pos]  //访问第pos位
a.test(pos)  //第pos位是否为1
a.set()  //全部设为1
a.reset()  //全部清零
a.flip()  //全部取反
a.to_ulong()  //转成32位无符号整数
```
或者也可以对bitset使用位操作（返回一个bitset）：
```
a|b
a&b
a^b
~a
a<<1
a>>1
```
这些都和整数的位操作类似，这里不做解释

bitset的原理
---------
相信大家都打过高精度吧
bitset的原理与高精度压位类似

即：将一个很长的01串按64位一组划分
每组01串用一个64位无符号整数记录

bitset所有的操作都是基于对整数的位操作实现的。
所以bitset的效率非常高，可以看作$O(N/64)$

bitset的应用
---------
由于bitset的高效率并且易于使用
所以一般用于各种暴力骗分（见前言）
但是，如果真的想要多拿一些分，可以考虑手写bitset，~~这样常数比较小~~

例题
--
[POJ2443](http://poj.org/problem?id=2443)

示例程序：

```
#include<cstdio>
#include<bitset>
using namespace std;
#define nc getchar
int n,q;
bitset<1002> S[10002];
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res*f;
}
int main(){
	n=red();
	for (int i=1;i<=n;i++)
	 for (int j=1,k=red();j<=k;j++)
	  S[red()].set(i);
	q=red();
	while (q--){
		int i=red(),j=red();
		if ((S[i]&S[j]).any()) printf("Yes\n");else printf("No\n");
	}
	return 0;
}
```