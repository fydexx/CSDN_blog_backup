##SAT及2-SAT
SAT是一类关于变量取值的问题
具体模型：有若干个变量，各变量之间有取值约束，判断是否有一个满足所有约束的可行解，或求出某个特殊解
特殊地，若所有变量的取值都只有两种可能，那么此问题被称为2-SAT问题
##2-SAT的解法
首先是建图。
对于2-SAT问题，我们一般先观察题目条件
把它抽象成约束关系，再进行建图。
建图时，一个变量的两个取值视为两个不同的点。

比如:$a|b$为真
可以得到：$!a\Rightarrow b$以及 $!b\Rightarrow a$
等价于a'->b，b'->a（->表示连边）

用有向图来表示条件之间的推出关系，是2-SAT的精髓

建图完毕后有两种解法
笔者习惯称之为“遍历法”与“分量法”
下面来看一下

---
###遍历法
前面讲到，建图就是把约束关系表示为图的形式
对于一条路径(s,t)，就表示条件s经过一系列的推导，可以推出条件t

显然若存在路径(i,i')，则i对应变量的取值不能为i
若又存在路径(i',i)，则无解

那么就可以枚举每个变量，用DFS遍历判断是否存在路径(i,i')与(i',i)

这样不仅可以判断是否有解，还可以得到一组特殊解（如字典序最小解）
（i出发能遍历的点都取对应的值即可）

显然时间复杂度为$O(NE)$
###分量法
通过上面的分析可以发现：
判断存在路径(i,i')与(i',i)，其实就是在判断i与i'是否在同一个强连通分量中

那么Tarjan缩点即可判断是否有解

如果要求一组解，就需要对缩点后的图进行拓扑排序
对于一个变量，取拓扑序较大点对应的取值即可
（从拓扑序大的点i出发一定走不到i'，就保证了一定可行）

其实动下脑子就会发现，根本不需要拓扑排序
因为Tarjan刷出SCC的顺序与原图拓扑序相反
这样只需要取SCC编号较小的取值即可

值得注意的是，此方法只适用于有对称性的图
（若建出的图没有对称性，很容易得到反例）

---
##例题
传说中的2-SAT练手6题：
POJ 3207
POJ 3683
POJ 3678
POJ 3648
POJ 2723
POJ 2749