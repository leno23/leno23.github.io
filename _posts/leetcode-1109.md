---
layout: post
title: Leetcode 1109. 航班预订统计
category: 算法
tags: [差分数组, 前缀和]
no-post-nav: true
---

### <center> 1109. 航班预订统计

这里有 n 个航班，它们分别从 1 到 n 进行编号。

有一份航班预订表 bookings ，表中第 i 条预订记录 bookings[i] = [firsti, lasti, seatsi] 意味着在从 firsti 到 lasti （包含 firsti 和 lasti ）的 每个航班 上预订了 seatsi 个座位。

请你返回一个长度为 n 的数组 answer，里面的元素是每个航班预定的座位总数。

示例 1：

输入：bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
输出：[10,55,45,25,25]
解释：
航班编号 1 2 3 4 5
预订记录 1 ： 10 10
预订记录 2 ： 20 20
预订记录 3 ： 25 25 25 25
总座位数： 10 55 45 25 25
因此，answer = [10,55,45,25,25]

```js
var corpFlightBookings = function (bookings, n) {
  // 类比成上下车问题
  // 记[a,b,c]=bookings，表示有c个人在a处上车，在a+1处下车

  // 在每个位置车上增加的人数
  // 数组下标从0开始，编号从1开始，所以需要初始化n+1个位置
  const cnt = new Array(n + 1).fill(0)
  for (const [a, b, c] of bookings) {
    cnt[a] += c
    if (b + 1 < n + 1) {
      cnt[b + 1] -= c
    }
  }
  // 从1开始到每个位置车上的人数
  for (let i = 1; i <= n; i++) {
    cnt[i] += cnt[i - 1]
  }
  // 编号从1开始
  return cnt.slice(1)
}
```
