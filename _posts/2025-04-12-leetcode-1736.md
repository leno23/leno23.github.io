---
title: Leetcode 1736. 替换隐藏数字得到的最晚时间
index_img: https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d3774d9643524d51a633a3e5be6950b7~tplv-k3u1fbpfcp-watermark.image
tags: [枚举]
categories: [算法]
comment: 'valine'
---


### <center> 1736. 替换隐藏数字得到的最晚时间

给你一个字符串 time ，格式为 hh:mm（小时：分钟），其中某几位数字被隐藏（用 ? 表示）。

有效的时间为 00:00 到 23:59 之间的所有时间，包括 00:00 和 23:59 。

替换 time 中隐藏的数字，返回你可以得到的最晚有效时间。

示例 1：

输入：time = "2?:?0"
输出："23:50"
解释：以数字 '2' 开头的最晚一小时是 23 ，以 '0' 结尾的最晚一分钟是 50 。


![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6b51171d4db1421d81bfd5bb9fa40138~tplv-k3u1fbpfcp-watermark.image)

```py
class Solution:
    def maximumTime(self, a: str) -> str:
        # 从最晚往前枚举每一个时间点
        for i in range(23,-1,-1):
            for j in range(59,-1,-1):
                t = f'{i//10}{i%10}:{j//10}{j%10}'
                ok = 1
                # 检查每一个字符是否符合条件
                for k in range(5):
                    # 某一位只有在time中是? 或者 在time中数字和当前时间的数字相同 才符合
                    if a[k] != '?' and a[k]!=t[k]:
                        ok = 0
                        break
                # 有符合条件的直接返回
                if ok:
                    return t
        return ""
```