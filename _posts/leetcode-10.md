---
title: Leetcode 10. 正则表达式匹配
index_img: https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3ba205d93a104d4a9b940e09479cd7ec~tplv-k3u1fbpfcp-watermark.image
tags: [动态规划,字符串]
categories: [算法]
comment: 'valine'
---

10. 正则表达式匹配

请实现一个函数用来匹配包括`'.'`和`'*'`的正则表达式。

模式中的字符`'.'`表示任意一个字符，而`'*'`表示它前面的字符可以出现任意次（含0次）。

在本题中，匹配是指字符串的所有字符匹配整个模式。

例如，字符串`"aaa"`与模式`"a.a"`和`"ab*ac*a"`匹配，但是与`"aa.a"`和`"ab*a"`均不匹配。

#### 数据范围

输入字符串长度 [0,300][0,300]。

#### 样例

```
输入：

s="aa"
p="a*"

输出:true
```

```js
/*
思路
动态规划
记f[i][j] 为p中前j个字符是否可以匹配s中前i个字符
初始化 f[0][0]=1代表空字符可以匹配空字符
b* a*b* 这种可以匹配空字符,即p中从开头连续若干个x*可以匹配空字符

状态递推：
根据p最后一位是否是* 分情况讨论
p[j-1]=='*'
    1.因为b*可以匹配空字符，故 if f[i][j-2]: f[i][j]=1
    2.*可以匹配s的最后一个字符，匹配成功的条件有两个: *前字符为. *前字符==s最后一个字符，即
        if f[i-1][j] and (p[j-2]=='.' or p[j-2]==s[i-1]): f[i][j]=1

p[j-1]!='*':
    这种情况下，只能是s[i-1] p[j-1]进行单字符对比，那么
    如果f[i-1][j-1]=1的话，如果是p[j-1] s[i-1]可以匹配成功的话，最终也可以匹配，即
    if f[i-1][j-1] and (s[i-1]==p[i-1] or p[i-1]=='.'): f[i][j]=1

根据以上递推逻辑，即可完成代码编写


 */ 
var isMatch = function (s, p) {
    // dp[i][j] p前j项是否可以匹配s的前i项
    let m = s.length, n = p.length
    let dp = Array(m + 1).fill().map(() => Array(n + 1).fill(0))
    dp[0][0] = 1
    // 初始化一些可以匹配空字符的状态
    for (let j = 2; j <= n; j += 2) {
        // b* a*b* ...等可以匹配空字符
        // p=xxxb*  如果xxx可以和""匹配，如果最后一个字符是*，整个p也可以和""匹配
        dp[0][j] = dp[0][j - 2] && p[j - 1] == '*'
    }
    // 递推过程看做从s p的第一个字符开始匹配，
    // 然后分别给sp追加一个字符之后，讨论匹配情况

    for (let i = 1; i <= m; i++) {
        for (let j = 1; j <= n; j++) {
            if (p[j - 1] == '*') {
                // b* 如果匹配空字符
                // xxxb* 如果xxx可以和s匹配，那么只要b出现0次即可匹配
                if (dp[i][j - 2]) dp[i][j] = 1
                /**
                如果p去掉*仍然可以和s匹配
                例如
                abc 可以用ab. 匹配，那么加上*,仍然能匹配的条件是
                *前面的字符要么是 . 要么和s的最后一个字符相同
                */ 
                else if (dp[i][j-1] && (s[i - 1] == p[j - 2] || p[j - 2] == '.')) dp[i][j] = 1
                
            } else {
                // 如果p前j-1位可以匹配s的前i-1位
                if (dp[i - 1][j - 1]){
                    // 那么p的最后一位是 . 或者和s最后一位相同都可以匹配成功
                    if (s[i - 1] == p[j - 1] || p[j - 1] == '.') dp[i][j] = 1
                }
            }
        }
    }

    return dp[m][n]
};


```
```py
class Solution(object):
    def isMatch(self, s, p):
        n,m=len(s),len(p)
        f=[[0]*(m+1) for i in range(n+1)]
        f[0][0]=1
        for i in range(2,m+1,2):
            f[0][i]=f[0][i-2] and p[i-1]=='*'
        for i in range(1,n+1):
            for j in range(1,m+1):
                if p[j-1]=='*':
                    if f[i][j-2]: f[i][j]=1
                    elif f[i-1][j] and (s[i-1]==p[j-2] or p[j-2]=='.'): f[i][j]=1
                else:
                    if f[i-1][j-1]:
                        if s[i-1]==p[j-1] or p[j-1]=='.': f[i][j]=1
        return bool(f[n][m])
```