---
layout: post
title: Leetcode 1094. 拼车
category: 算法
tags: [贪心, 差分数组, 前缀和]
no-post-nav: true
---

### <center> 1094. 拼车

车上最初有 capacity 个空座位。车 只能 向一个方向行驶（也就是说，不允许掉头或改变方向）

给定整数 capacity 和一个数组 trips , trip[i] = [numPassengersi, fromi, toi] 表示第 i 次旅行有 numPassengersi 乘客，接他们和放他们的位置分别是 fromi 和 toi 。这些位置是从汽车的初始位置向东的公里数。

当且仅当你可以在所有给定的行程中接送所有乘客时，返回 true，否则请返回 false。

示例 1：

输入：trips = [[2,1,5],[3,3,7]], capacity = 4
输出：false

```js
// 贪心
// 借助优先队列，按照乘客上车的时间从小到大排序之后，每次上车之前，把之前时间下车的乘客去掉，
// 在这个过程中统计车上的总人数，如果某次上车之后，总人数超过capacity，则返回false;否则，最终返回true
/**
 * @param {number[][]} trips
 * @param {number} capacity
 * @return {boolean}
 */
var carPooling = function (trips, capacity) {
  trips.sort((a, b) => a[1] - b[1])
  let q = new PriorityQueue({
      compare: (a, b) => a[2] - b[2]
    }),
    cap = 0
  for (let [a, b, c] of trips) {
    while (q.size() && q.front()[2] <= b) {
      let top = q.front()
      cap -= top[0]
      q.dequeue()
    }
    cap += a
    if (cap > capacity) return false
    q.enqueue([a, b, c])
  }

  return true
}

// 差分
// 借助哈希表
// 由于总路程最多1000,所以创建一个大小为1001的数组记录在每个位置时车上的人数
// 遍历数组a，添加某个位置的上车人数，减掉某个位置的下车人数，就得到了每个位置增加的人数
// 最终在a的前缀和数组中，遍历每个位置，如果没有存在某个位置人数超过capacity，则返回true;否则，返回false
/**
 * @param {number[][]} trips
 * @param {number} capacity
 * @return {boolean}
 */
var carPooling = function (trips, capacity) {
  let cnt = Array(1001).fill(0)
  for (let [a, b, c] of trips) {
    cnt[b] += a
    cnt[c] -= a
  }
  let sum = 0
  for (let i = 0; i < 1001; i++) {
    sum += cnt[i]
    if (sum > capacity) return false
  }
  return true
}
```
