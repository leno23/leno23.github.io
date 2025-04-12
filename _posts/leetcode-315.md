---
title: Leetcode 315. 计算右侧小于当前元素的个数
index_img: https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f6588a6bf74423588fefd977b2dbaeb~tplv-k3u1fbpfcp-watermark.image
tags: [前缀和,树状数组,归并排序,分治]
categories: [算法]
comment: 'valine'
---

### <center> 315. 计算右侧小于当前元素的个数

给你一个整数数组 nums ，按要求返回一个新数组 counts 。数组 counts 有该性质： counts[i] 的值是  nums[i] 右侧小于 nums[i] 的元素的数量。

 

示例 1：

输入：nums = [5,2,6,1]
输出：[2,1,1,0] 
解释：
5 的右侧有 2 个更小的元素 (2 和 1)
2 的右侧仅有 1 个更小的元素 (1)
6 的右侧有 1 个更小的元素 (1)
1 的右侧有 0 个更小的元素

```js
// 方法一： 树状数组思路，类似计数排序，只不过需要快速计算前缀和
var countSmaller = function (nums) {
    let n = nums.length
    let a = [...new Set(nums)].sort((a, b) => a - b)
    /* [1,2,2,7,8,5]
    由于需要求一个数字右侧小于他的数字个数
    所以，可以按照数字大小，从右往左统计每个数字出现的次数
    1 2 5 7 8
    0 0 1 0 0     5出现一次，小于5的数字出现次数为0
    0 0 1 0 1     8出现一次，小于8的数字出现次数为1，即1 2 5 7出现次数之和 0+0+1+0=1
    0 0 1 1 1     7出现一次，小于7的数字出现次数为1，即1 2 5出现次数之和 0+0+1 = 1
    0 1 1 1 1     2出现一次，小于2的数字出现次数为0
    0 2 1 1 1     2出现两次，小于2的数字出现次数为0
    1 2 1 1 1     1出现1次，小于1的数字出现次数为0

    统计答案 [0,0,0,1,1,0]
    */ 
    // 树状数组第0位不存储数据 
    let c = Array(n + 1).fill(0)
    const lowbit = x => x & (-x)
    const query = x => { let r = 0; while (x) { r += c[x], x -= lowbit(x) } return r }
    const update = x => { while (x < c.length) c[x]++, x += lowbit(x) }
    // 数组经过去重顺序处理后，查询某个数字的位置，由于经过排序可以使用二分查找
    const getId = x => {
        let l = 0, r = a.length - 1
        while (l < r) {
            let mid = l + r >> 1
            if (x > a[mid]) l = mid + 1
            else r = mid
        }
        return l
    }
    let res = []
    for (let i = n - 1; i >= 0; i--) {
        let id = getId(nums[i]) + 1
        res[i] = query(id - 1)
        update(id)
    }
    return res
};

// 方法二 归并排序+索引数组

var countSmaller = function (nums) {
    let n = nums.length, tmp = Array(n).fill(0), index = [], tmpIndex = [], res = Array(n).fill(0)
    for (let i = 0; i < n; i++) index[i] = i, tmpIndex[i] = i
    /* 52631
    借助 归并排序，在合并52  和 631两个子序列时，如下分析
    
    归并过程  5    2         6  3  1
             p1   mid          p2  r
     tmp数组       6
     index   0     1        2  3  4
    由于此时5>3，而左右数组已经是降序排序，所以 p2~r的数字都小于 5,个数为 r-p2+1
    但是，由于排序之后5在原数组中位置发生变化，所以需要使用索引数组index记录每个数字的原始位置           
    然后，在每次归并过程中将数字排序前的位置记录到tmpIndex中，便于后续归并后更新位置
    */
    
    const merge_sort = (l, r) => {
        if (l == r) return
        let mid = l + r >> 1
        merge_sort(l, mid)
        merge_sort(mid + 1, r)
        let p1 = l, p2 = mid + 1, k = l
        while (p1 <= mid || p2 <= r) {
            if (p2 > r || p1 <= mid && nums[p1] > nums[p2]) {
                tmp[k] = nums[p1]
                // 记录每个数字排序前的位置
                tmpIndex[k] = index[p1]
                res[index[p1]] += r - p2 + 1
                p1++
            } else {
                tmp[k] = nums[p2]
                // 记录每个数字排序前的位置
                tmpIndex[k] = index[p2]
                p2++
            }
            k++
        }
        for (let i = l; i <= r; i++) index[i] = tmpIndex[i], nums[i] = tmp[i]
    }
    merge_sort(0, n - 1)
    return res
};
```