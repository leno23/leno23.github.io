---
title: Leetcode 309. 最佳买卖股票时机含冷冻期
index_img: https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/78270e481a9b4472baaca8e62309c758~tplv-k3u1fbpfcp-watermark.image
tags: [动态规划]
categories: [算法]
comment: 'valine'
---


### <center> 309. 最佳买卖股票时机含冷冻期
```

给定一个整数数组`prices`，其中第 **`prices[i]` 表示第 `i` 天的股票价格 。​

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

-   卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**注意：** 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
```
 

**示例 1:**

```
输入: prices = [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```

**示例 2:**

```
输入: prices = [1]
输出: 0
```

```py
'''
同样是一道股票类问题，由于加入了冷冻期，需要在通用的状态转移方程基础上稍作修改
股票问题的通用状态转移方程为
    f[i][0]=max(f[i-1][0],f[i-1][1]+p[i-1])
    f[i][1]=max(f[i-1][1],f[i-1][0]-p[i-1])

观察式子2，在买入股票时，由于有冷却期，那么第i-1天卖出后，第i天是不能购买股票的。
也就是计算f[i][1]时，不能够使用f[i-1][0]来转移，因为昨天买入的今天刚进入冷却期，而冷却期有一天，所以这里需要从i-2天卖出的状态进行转移，式子变为
    f[i][1]=max(f[i-1][1],f[i-2][0]-p[i-1])
    使用更新后的状态转移方程即可，其他地方和之前一样
'''
class Solution:
    def maxProfit(self, p: List[int]) -> int:
        n=len(p)
        '''
        '''
        f=[[0,0]for i in range(n+1)]
        f[1][1]=-p[0]        
        for i in range(2,n+1):
            f[i][0]=max(f[i-1][0],f[i-1][1]+p[i-1])
            f[i][1]=max(f[i-1][1],f[i-2][0]-p[i-1])
        return f[n][0]


# 滚动数组  空间优化
class Solution:
    def maxProfit(self, p: List[int]) -> int:
        n=len(p)
        '''
        从上式子可以看到f[i][0] f[i][1]只依赖与前两天的最大收益，故可以使用滚动数组进一步优化

        记f[i-1][0] 为i_0  f[i-1][1]为i_1 f[i-2][0]为i_2
        变量之间的转化关系如下
        i-2     i-1         i
         0    0    1    nex0  nex1
        i_2   i_0 i_1
              i_2        i_0   i_1
        '''
        i_1=-p[0]
        i_0,i_2=0,0
        for i in range(2,n+1):
            nex0=max(i_0,i_1 +p[i-1])
            nex1=max(i_1,i_2-p[i-1])
            i_2=i_0
            i_0=nex0
            i_1=nex1
        return i_0

```
        