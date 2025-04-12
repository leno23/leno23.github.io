---
layout: post
title: Leetcode 300. 最长递增子序列
category: 算法
no-post-nav: true
---

### <center> 300. 最长递增子序列

给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列 是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

 
示例 1：

输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。

### 线段树

```js

var lengthOfLIS = function (nums) {

    // dp[i][j] 为前i个数字，结尾为j的数字的 最长递增子序列的长度
    // dp[i][j]可以由 0~i-1中dp[i-1][j'] 转移过来，其中 j' < j
    // 又因为前i个数字的状态都是由前i-1项转移过来的，所以在我们从前往后遍历过程中，可以省略这个维度
    // 
    n = Math.max(...nums) + 10001

    // 开4*n的原因
    // 需要维护的区间个数是  m时(m是2的n次方) 需要的节点个数是 1+2+4+...+m = 2*m-1
    // 如果m不是2的n次方，例如m=10，维护的区间如下图所示，可以发现他不是满二叉树，
    // 那么，我们需要覆盖所有层，也就是覆盖最后一个不完整的层，那么我们就需要知道最后一层的节点个数 
    // 我们可以先求出上一层满节点的个数 应该是log(m)，那么下一层的节点个数就是2*log(m)
    // 所以，此时将整个树看成满二叉树时，需要的节点个数是 2*(2*log(m)) - 1 = 4*m -1
    // 又，线段树从1开始计数，所以节点个数还需要增加1，也就是 4*m -1 + 1 = 4*m

    /*        1
           /      \
          2        3         m=2 需要维护3个节点
         / \      /   \   
        4   5    6     7     m=4 需要维护7个节点
       / \ / \  / \    / \ 
       8 910 11 12 13 14 15   m=8 需要维护15个节点

       // m+10    
       1                           10
       |___________________________|                   1-10                                  1
       1           56                                /      \                  
       |___________||______________|              1-5        6-10                        2        3
       1    34     56      89      10            /  \       /   \
       |____||_____||______||______|            1-3 4-5    6-8  9-10                   4   5     6     7   
       1 2 3  4  5  6  7 8   9 10               / \  / \  / \    / \               
       |_||_||_||_| |__||_| |_||_|           1-2  3  4 5 6-7 8  9  10                 8 9 10 11 12 13 14 15
      1  2          6  7                     / \         / \         
     |_||_|        |_||_|                   1  2        6   7                      16 17          
    */
    let mx = Array(4 * n).fill(0)

    // o表示当前节点, [l, r]表示当前节点对应区间, idx加到哪, val加什么值
    // modify(1, 1, n, idx, val) 第一个一表示从根节点进去，区间为[1, n], 以及要插入的idx和val
    function modify(o, l, r, idx, val) {
        if (l == r) {
            mx[o] = val
            return
        }
        m = (l + r) >> 1
        if (idx <= m) modify(2 * o, l, m, idx, val)
        else modify(2 * o + 1, m + 1, r, idx, val)
        mx[o] = Math.max(mx[o * 2], mx[o * 2 + 1])
    }

    //  l,r对应当前区间, L和R对应query区间
    //  self.modify(1, 1, n, idx, val) 第一个一表示从根节点进去，区间为[1, n], 以及要查询的[L, R]
    function query(o, l, r, L, R) {
        if (L <= l && r <= R) {
            return mx[o]
        }
        res = 0
        m = (l + r) >> 1
        if (L <= m) res = query(o * 2, l, m, L, R)
        if (R > m) res = Math.max(res, query(o * 2 + 1, m + 1, r, L, R))
        return res

    }
    for (let i=0;i<nums.length;i++) {
        nums[i] += 10001 // 这里就是保证了nums均为正值，然后采用正常逻辑query和modify即可。
        // 查询前i-1项数字中以小于x结尾的最长递增子序列的长度
        res = 1 + query(1, 1, n, 1, nums[i] - 1)
        // 更新结尾在1-nums[i]范围的最长递增子序列
        modify(1, 1, n, nums[i], res)
    }
    return mx[1]
};
```
### 动态开点线段树
```js

/**
 * @param {number[]} nums
 * @return {number}
 */
class Node {
    constructor() {
        this.left = this.right = null
        this.val = this.lazy = 0
    }
}
let N = 1e9
class SegTree {
    constructor() {
        this.root = new Node()
    }
    query(l, r) {
        return this._query(this.root, 0, N, l, r)
    }
    _query(node, left, right, l, r) {
        if (l <= left && right <= r) return node.val
        this.pushDown(node)
        let mid = left + right >> 1
        let res = 0
        if (l <= mid) res = this._query(node.left, left, mid, l, r)
        if (r > mid) res = Math.max(res, this._query(node.right, mid + 1, right, l, r))
        return res
    }
    pushDown(node) {
        if (node.left == null) node.left = new Node()
        if (node.right == null) node.right = new Node()
        if (node.lazy == 0) return
        node.left.val = node.lazy
        node.right.val = node.lazy
        node.left.lazy = node.lazy
        node.right.lazy = node.lazy
        node.lazy = 0
    }
    pushUp(node) {
        node.val = Math.max(node.left.val, node.right.val)
    }
    update(l, r, val) {
        this._update(this.root, 0, N, l, r, val)
    }
    _update(node, left, right, l, r, val) {
        if (l <= left && right <= r) {
            node.val = val
            node.lazy = val
            return
        }
        this.pushDown(node)
        let mid = left + right >> 1
        if (l <= mid) this._update(node.left, left, mid, l, r, val)
        if (r > mid) this._update(node.right, mid + 1, right, l, r, val)
        this.pushUp(node)
    }
}
var lengthOfLIS = function (nums) {
    let tr = new SegTree(), res = 0
    // f[i]以数字i为结尾的最长自增子序列的长度
    // 10 9 2 5 3 7 101 18
    /* 递推过程
    从前往后一次遍历数组
    10    
        看前面有没有以小于10结尾的序列，发现没有,返回1
    10 9 
        1 看前面有没有以小于9结尾的序列，发现没有,返回1 
    10 9 2
        看前面有没有以小于2结尾的序列，发现没有,返回1 
    10 9 2 5  
        看前面有没有以小于5结尾的序列，发现2,返回max(f[2]+1,f[5]) = 2
    10 9 2 5 3
        看前面有没有以小于3结尾的序列，发现2,返回max(f[2]+1,f[3]) = 2
    10 9 2 5 3 7
        看前面有没有以小于7结尾的序列，发现2,5,3,尝试将2、5、3结尾的序列和7连接，比较形成的递增子序列的长度,27 257 237,返回最大长度3
    10 9 2 5 3 7 101
        看前面有没有以小于101结尾的序列，发现2,5,3,7尝试将2、5、3、7结尾的序列和7连接，比较形成的递增子序列的长度,返回最大长度4
    10 9 2 5 3 7 101 18
        看前面有没有以小于18结尾的序列，发现2,5,3,7尝试将2、5、3、7结尾的序列和7连接，比较形成的递增子序列的长度,返回最大长度4
    */
    for (let x of nums) {
        x += 10005
        let len = tr.query(0, x - 1) + 1
        tr.update(x, x, len)
        if (len > res) res = len
    }
    return res
};
```