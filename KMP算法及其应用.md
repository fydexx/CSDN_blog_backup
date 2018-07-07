前言
--
今天学习了一个新算法：KMP算法
~~其实很久以前学过早忘了~~
KMP算法是用于处理字串问题的算法。

参考Matrix67的博客：[KMP算法详解|Matrix67](http://www.matrix67.com/blog/archives/115#comment-1174546)

KMP算法的原理
--
假设有字符串A和B，要求判断B是否是A的字串
其实就是对于每个i，求最大的j，使得$A_{i-j+1\rightarrow i}与B_{1\rightarrow j}$一一匹配
能匹配j指针就往后跳一个
否则就需要往回退
如下图：
![这里写图片描述](http://img.blog.csdn.net/20170801202513988?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
我们希望往回退得越少越好，
通过上图可以发现，其实最佳方案就是 最长相同前后缀
对字符串B的每个前缀字串，找一个最长的长度l，使得长度为l的前后缀相同
这个可以预处理出来，第i个前缀的长度l记为next[i]
那么每次不匹配就往回退next[j]即可
只要出现j==m的情况就说明B是A的字串

代码如下：

```
for (int i=1,j=0;i<=n;i++){
	while (j>0&&B[j+1]!=A[i]) j=nxt[j];
	if (B[j+1]==A[i]) j++;
	if (j==m){
		//do something……
	}
}
```

---
下面来说预处理的事。
怎样得到next[]？假设已经得到next[1~i-1]，现在求next[i]
设next[i-1]为j，如果B[j+1]==B[i]，那么next[i]=next[i-1]+1
否则需要缩小j的范围。
![这里写图片描述](http://img.blog.csdn.net/20170801204037460?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlua2ZxeQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
有读者可能会发现，寻找next[i]的过程其实就是将B[1~i-1]与B[]匹配的过程
所以回退next[j]即可

代码如下：

```
nxt[1]=0;
for (int i=2,j=0;i<=n;i++){
	while (j>0&&B[j+1]!=B[i]) j=nxt[j];
	if (B[j+1]==B[i]) j++;
	nxt[i]=j;
}
```

---
接下来证明复杂度。
通过上面的代码可以发现，j指针最多向后跳n次
而j在任何时候都是大于0的，所以回退也是O(n)级别的
所以KMP算法的复杂度是O(n)

KMP算法的应用
--------
通过next[]数组，可以得到一个字符串的最小周期以及最小循环节
最小周期为n-next[n]，如果周期能整除n，最小循环节就是周期
否则是n