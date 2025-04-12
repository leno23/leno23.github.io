---
layout: post
title: Leetcode 1234 替换子串得到平衡字符串
category: 算法
tags: [滑动窗口, 二分]
no-post-nav: true
---

### <center> 1234.  替换子串得到平衡字符串

有一个只含有  `'Q', 'W', 'E', 'R'`  四种字符，且长度为  `n`  的字符串。

假如在该字符串中，这四个字符都恰好出现  `n/4`  次，那么它就是一个「平衡字符串」。



给你一个这样的字符串  `s`，请通过「替换一个子串」的方式，使原字符串  `s`  变成一个「平衡字符串」。

你可以用和「待替换子串」长度相同的  **任何**  其他字符串来完成替换。

请返回待替换子串的最小可能长度。

如果原字符串自身就是一个平衡字符串，则返回  `0`。



**示例 1：**

```
输入： s = "QWER"
输出： 0
解释： s 已经是平衡的了。
```

```py
''' 滑窗：
记d=len(s)//4
如果在某个子串之外存在某个字符个数 >= d，因为替换后字符个数不会减少，则当前子串替换后不能满足 QWER字符个数都等于d
也就是说，只有某个子串之外的字符个数都 <=d，才会替换成功
借助滑动窗口，用r 从左到右枚举滑动的每个右边界。
初始化记录s中每个字符的个数 c
首先，从右侧进入一个字符s[r]，窗口外s[r]字符减少一个，更新窗口外字符个数，也就是c[s[r]]-=1
然后，判断此时c中每种字符个数是否满足<=d
    如果满足，则使用当前滑窗l+1~r长度 r-l+1来更新答案，并且从左侧缩小窗口范围，把窗口左侧字符s[l]踢出窗口,判断是否仍然满足...一直到不满足条件。
    不满足，则尝试从右侧进入更多字符
'''
class Solutioin:
    def balancedString(self, s):
        # 记录滑窗之外的每个字符的出现个数
        c=Counter(s)
        d=len(s)//4
        if all(c[x]==d for x in 'QWER'): return 0
        l,r=0,0
        ans = len(s)
        while r<len(s):
            c[s[r]]-=1
            # l+1~r部分为将要替换的部分
            while all(c[x]<=d for x in 'QWER'):
                ans = min(ans,r-l+1)
                c[s[l]]+=1
                l+=1
            r+=1
        return ans

'''
二分+滑窗+计数
根据上面的分析，我们知道s中某个子串如果满足它之外的字符个数都<= d，则当前子串的长度就是满足条件的 一个答案
根据直觉我们还知道，如果s的长度越长，那么答案是会增加的，也就是满足条件的子串的长度会随着s的增加而增加
根据这个单调性，我们就可以从小到大对 子串长度进行二分。
如果一个长度len下存在 某个子串，s中在它之外的字符个数都<=d，则len符合条件
又因为，我们是从小到大枚举的，所以得到的答案就是最小的答案了。
'''
class Solution:
    def balancedString(sef, s: str) -> int:
        c=Counter(s)
        n=len(s)
        d=n//4
        # 当前字符已经满足条件
        if all(c[x]==d for x in 'QWER'): return 0
        def check(limit):
            inner = Counter(s[:limit])
            outer = c-inner
            # 窗口从0位置 向右移动寻找是否有满足条件的子串
            # 枚举窗口的左边界
            for i in range(n-limit+1):
                # 当前位置是否满足条件
                if all(outer[x]<=d for x in 'QWER'):
                    return 1
                # 左边界字符从窗口出去
                inner[s[i]]-=1
                outer[s[i]]+=1
                # 是否存在右边界字符
                if i+limit<n:
                    # 右边界字符进入窗口
                    inner[s[i+limit]]+=1
                    outer[s[i+limit]]-=1
            return 0
        # 枚举可能的子串长度 1~n-1
        l,r=1,n-1
        while l<r:
            m=l+r>>1
            if not check(m):
                l=m+1
            else: r=m
        return l

```
