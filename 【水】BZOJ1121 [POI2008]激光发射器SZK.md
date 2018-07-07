#【水】BZOJ1121 [POI2008]激光发射器SZK

[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1121)



由于从一个顶点出发，最后一定会到另一个顶点

所以答案就是$\frac n2 $



示例程序：

```C++
#include<cstdio>

int main(){
	int n;
	scanf("%d",&n);
	printf("%d",n/2);
	return 0;
}
```

