---
title: Leetcode 1534. 统计好三元组
index_img: https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/87eb4646642547548c5465f28a97433f~tplv-k3u1fbpfcp-watermark.image
tags: [频次数组,前缀和]
categories: [算法]
comment: 'valine'
---



今天我们来做一下leetcode上面的一道比较经典字符串类动态规划题目[1534. 统计好三元组](https://leetcode.cn/problems/count-good-triplets/)这道题目。

虽然是一道简单题，由于数据量小，使用暴力的O(n^3)可以解决。但是注意到官解提供了更优的O(n^2)解法。

深入学习之后，发现其中对于频次数组使用前缀和统计数组在指定范围数字个数的思路很受启发，所以有这篇文章，将学习过程分享给大家，一起学习，一起进步~✌


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9b7c38bc2949424d9cc571a1fc8605fe~tplv-k3u1fbpfcp-watermark.image?)

### 解题思路

-   一般，我们使用频次数组用于记录数组中每个数字出现的次数，例如  
    记录arr中每个数字出现的个数 arr[i]∈[0,1000]

```js
let arr = [3,0,1,1,9,7],sum = Array(1001).fill(0)
for(let i=0;i<arr.length;i++>){
    sum[arr[i]]++
}
```

-   如果，我们需要记录小于等于arr[i]的数字的个数呢？ 这时上面的方法就需要特殊修改下

```js
let arr = [3,0,1,1,9,7],sum = Array(1001).fill(0)
for(let i=0;i<arr.length;i++;){
    for(let k=arr[i];i<1001;k++){
        sum[arr[i]]++
    }
    /* 上面的代码中由于我们将大于等于arr[i]的数字的个数都+1，
     也就是每出现一个arr[i]，所有大于等于arr[i]的数字个数都会增加1，
     这时sum[i]的个数就不仅仅包含arr[i]出现的个数，还包含小于arr[i]的数字的个数
    */
}
```

-   然后，我们就可以使用修改后的频次数组实现统计 arr[i]在指定范围l~r中的个数了

    -   首先，需要将问题稍微转化下，arr[i]在l~r中的个数 = 小于等于r的个数 而且 大于等于l的个数 = 小于等于r的个数 - 小于等于l-1的个数
    -   由于arr[i]是整数，那么小于等于r的数字中，要么大于等于l，要么小于等于l-1，那么在小于等于r的数字中，减掉小于等于l-1的部分，剩下的就是大于等于l，统计又小于等于r了

```js
let arr = [3,0,1,1,9,7],sum = Array(1001).fill(0)
for(let i=0;i<arr.length;i++;){
    // 在这里，就可以通过sum[x]可以拿到0~i-1中小于x的数字个数
    // 求[l-r]范围的数字个数？
    res += sum[r] - sum[l-1]


    for(let k=arr[i];i<1001;k++){
        sum[arr[i]]++
    }
}
```

-   通过上面对使用频次数组统计[l,r]范围数字的个数的分析，就可以得到如下官解的代码

### [](https://leetcode.cn/problems/count-good-triplets/solution/guanjie-by-wuyangfan-qv3o/#%E4%BB%A3%E7%A0%81)代码

```js
var countGoodTriplets = function (arr, a, b, c) {
    const n = arr.length, sum = new Array(1001).fill(0);
    let ans = 0;
    // sum频数数组记录 sum[i]数字i出现的次数
    const { min, max } = Math
    for (let j = 0; j < n; ++j) {
        for (let k = j + 1; k < n; ++k) {
            // sum[x] 是 0~j-1中小于等于x的数字个数
            if (Math.abs(arr[j] - arr[k]) > b) continue
            const l = max(0, max(arr[j] - a, arr[k] - c)),
                r = min(1000, min(arr[j] + a, arr[k] + c));
            if (l > r) continue
            // 0~j-1中arr[i]在l~r范围的数字个数，相当于
            // arr[i]小于等于r的个数  - arr[i]大于l-1的个数
            // sum[r] - sum[l-1]
            if (l == 0) ans += sum[r];
            else ans += sum[r] - sum[l - 1];
        }
        // 在统计数目之后记录频次，保证统计数目时 sum[i]中 i<j
        // sum[arr[j]]+=1 只是记录arr[j]出现的次数
        // 技巧：如果大于等于arr[j]的数字频率都加一，sum[i]就代表小于等于arr[j]的数字个数
        for (let k = arr[j]; k <= 1000; ++k) {
            sum[k] += 1;
        }
    }
    return ans;
};
```
