# 18th zhejiang provincial contest

# A

求和，判断大小即可。

# M

qrzgg tql~

恒为0。

思考，如何证明？

对于一个学生，可以容易发现期望是0，因为n个学生之间是相互独立的，所以总和也是0。

咕咕咕。

# L

易知，如果模版串 `T` 的第一个字符与其他字符相同，就会导致错误的发生。

所以只需要判断其他字符是否与首字符相同即可。

# C

lymgg tql~

qrzgg tql~

lymgg 给的神级思路，我直接吹爆。

易知如果是正方体。
我们将两两顶点相连，得到的线段长度的所有种类一共只有三种。

且满足比例 ${x_{1}}^{2} : {x_{2}}^{2} : {x_{3}}^{2} = 1 : 2 : 3$

# J

dijkstra() 套一个完全背包。

代价就是最短路的二倍，价值就是给出的价值。

数据范围很小，直接算即可。

# F

数论分块。

首先根据数据范围 $1e8$ ，我们可以考虑根号分治。

当最终分配给的人数小于等于 $10000$ 时候，我们可以从 $1-10000$ 枚举分配给了多少个人，然后考虑贪心的分配，计算出 $m$ 个物品分配给这些人，每个人获取到物品的最小值，统计答案；

当最终分配给的人数大于 $10000$ 的时候，我们可以考虑每个人分配到了多少个物品，每个人分配给的物品个数不会特别大，考虑枚举到10000，然后考虑贪心的分配，先优先删除人数，再添加物品，计算出答案即可。

by qrzgg 

小故事，赛时qrzgg通过了出题组的数据，然后被lymgg hack了。然后我们想起了区预赛某场比赛的赛后重测。


# I

gugugu ~


# G

开一个 `map` 离散化。

再开一个并查集维护一下合并即可。

注意到要维护集合内合并的次数。


# D

lymggtql

貌似是 `01trie`


