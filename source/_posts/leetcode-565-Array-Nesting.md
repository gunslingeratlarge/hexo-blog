title: leetcode 565. Array Nesting
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
categories:
  - 刷题
date: 2018-04-07 20:40:00
---
### [565\. Array Nesting](https://leetcode.com/problems/array-nesting/description/)

Difficulty: **Medium**



A zero-indexed array A of length N contains all integers from 0 to N-1\. Find and return the longest length of set S, where S[i] = {A[i], A[A[i]], A[A[A[i]]], ... } subjected to the rule below.

Suppose the first element in S starts with the selection of element A[i] of index = i, the next element in S should be A[A[i]], and then A[A[A[i]]]… By that analogy, we stop adding right before a duplicate element occurs in S.
<!--more-->
**Example 1:**  

``` 
Input: A = [5,4,0,3,1,6,2]
Output: 4
Explanation: 
A[0] = 5, A[1] = 4, A[2] = 0, A[3] = 3, A[4] = 1, A[5] = 6, A[6] = 2.

One of the longest S[K]:
S[0] = {A[0], A[5], A[6], A[2]} = {5, 6, 2, 0}
```

**Note:**  

1.  N is an integer within the range [1, 20,000].
2.  The elements of A are all distinct.
3.  Each element of A is an integer within the range [0, N-1].



#### Solution
``` java
/**
 * @param {number[]} nums
 * @return {number}
 */
var arrayNesting = function(nums) {
    var res = 0;
    for (var i = 0; i < nums.length; i++) {
        var count = 0;
        var start = i;
        while (nums[start] >= 0) {
            count++;
            var temp = nums[start];
            if (nums[start]!= 0) {
                nums[start] = -nums[start];

            }else {
                nums[start] = -999999;
            }
            start = temp;
          
        }
        res = Math.max(res,count);
    }
    return res;
};
```
思路：如果一个数组里的元素跟下标关系密切，比如n个元素，数组元素为0到n-1这种，就可以用这种方法来确定哪个元素被访问过了，不用使用多余的空间。这道题还有一个地方就是要注意，置为负数的时候要注意对0的处理。  
此外，对于这种循环形式的一组数，其实从循环的任何一个位置开始都可以访问得到所有的元素的，有一点像循环群，不是吗？