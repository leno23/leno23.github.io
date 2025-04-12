---
layout: post
title: Leetcode 795. 区间子数组个数
category: 算法
tags: [单调栈, 枚举]
no-post-nav: true
---

### <center> 795. 区间子数组个数

给你一个整数数组 nums 和两个整数：left 及 right 。找出 nums 中连续、非空且其中最大元素在范围 [left, right] 内的子数组，并返回满足条件的子数组的个数。

生成的测试用例保证结果符合 32-bit 整数范围。

示例 1：

输入：nums = [2,1,4,3], left = 2, right = 3
输出：3
解释：满足条件的三个子数组：[2], [2, 1], [3]

```js
// 单调栈
/**
 * @param {number[]} nums
 * @param {number} left
 * @param {number} right
 * @return {number}
 */
var numSubarrayBoundedMax = function (
  nums,
  left,
  right
) {
  let n = nums.length
  let st = []
  let l = Array(n).fill(-1),
    r = Array(n).fill(n)
  for (let i = 0; i < n; i++) {
    while (
      st.length &&
      nums[st[st.length - 1]] <= nums[i]
    )
      st.pop()
    if (st.length) l[i] = st[st.length - 1]
    st.push(i)
  }
  while (st.length) st.pop()
  for (let i = 0; i < n; i++) {
    while (
      st.length &&
      nums[st[st.length - 1]] <= nums[i]
    ) {
      r[st[st.length - 1]] = i
      st.pop()
    }
    st.push(i)
  }
  let res = 0
  for (let i = 0; i < n; i++) {
    if (nums[i] <= right && nums[i] >= left) {
      res += (r[i] - i) * (i - l[i])
    }
  }
  return res
}
// 枚举
var numSubarrayBoundedMax = function (
  nums,
  left,
  right
) {
  const cnt = (max) => {
    // 如何计算最大值小于等于max的子数组个数?
    // 枚举以每个数字结尾的子数组
    //                                  最大值              满足的子数组个数
    // 以2结尾的子数组    2                  1                     1
    // 以1结尾的子数组    2 21               2                     1+2
    // 以4结尾的子数组    4 14 214           4
    // 以3结尾的子数组    3 43 143 2143      4
    let cur = 0,
      res = 0
    for (let x of nums) {
      if (x <= max) {
        res += ++cur
      } else cur = 0
    }
    return res
  }
  return cnt(right) - cnt(left - 1)
}
```
