---
title: acwing 4366. 上课睡觉
index_img: https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/084e4a30fb8c4bc3bef0b57766c9eec0~tplv-k3u1fbpfcp-watermark.image
tags: [数学]
categories: [AcWing,算法]
comment: 'valine'
---


### <center> 4366. 上课睡觉


有 NN 堆石子，每堆的石子数量分别为 a1,a2,…,aNa1,a2,…,aN。

你可以对石子堆进行合并操作，将两个相邻的石子堆合并为一个石子堆，例如，如果 a=[1,2,3,4,5]a=[1,2,3,4,5]，合并第 2,32,3 堆石子，则石子堆集合变为 a=[1,5,4,5]a=[1,5,4,5]。

我们希望通过尽可能少的操作，使得石子堆集合中的每堆石子的数量都相同。

请你输出所需的最少操作次数。

本题一定有解，因为可以将所有石子堆合并为一堆。

#### 输入格式

第一行包含整数 TT，表示共有 TT 组测试数据。

每组数据第一行包含整数 NN。

第二行包含 NN 个整数 a1,a2,…,aNa1,a2,…,aN。

#### 输出格式

每组数据输出一行结果。

#### 数据范围

1≤T≤101≤T≤10,  
1≤N≤1051≤N≤105,  
0≤ai≤1060≤ai≤106,  
∑i=1nai≤106∑i=1nai≤106,  
每个输入所有 NN 之和不超过 105105。

#### 输入样例：

```
3
6
1 2 3 1 1 1
3
2 2 3
5
0 0 0 0 0
```

#### 输出样例：

```
3
2
0
```

#### 样例解释

第一组数据，只需要用 33 个操作来完成：

```
   1 2 3 1 1 1
-> 3 3 1 1 1
-> 3 3 2 1
-> 3 3 3
```

第二组数据，只需要用 22 个操作来完成：

```
   2 2 3
-> 2 5
-> 7
```

第三组数据，我们什么都不需要做。



```py
t=int(input())
'''
记 sum为石子的总数

通过操作最终每堆石子的数量最多是 所有石子的数量，最少是其中最多的一堆的数量，而且是sum的因子
通过尝试每一种数量x，依次对连续的石子数量求和sm，如果和sm==x，则将x置为0继续计算;若sm>x,则说明当前的x不符合条件，重新尝试下一个石子数量
最终得到答案
'''

def solve():
    n=int(input())
    a = [int(x) for x in input().split()]
    
    mx = max(a)
    sm = sum(a)
    if mx==0:
        print('0')
        return
    
    def check(y:int):
        # 数组中一段连续数字的和
        tsum = 0
        for x in a:
            tsum+= x
            # 连续数字的和等于y，则可以通过操作变成y
            if tsum==y:
                # 复原tsum
                tsum=0
            elif tsum > y:
                return False
        return True
    for y in range(mx,sm+1):
        # 经过操作之后，数组每一项是否可以变为y
        if sm % y==0 and check(y):
            print(n-sm//y)
            return

while t:
    solve()
    t-=1
```