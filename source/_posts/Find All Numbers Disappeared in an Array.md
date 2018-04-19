title: leetcode 448. Find All Numbers Disappeared in an Array
author: gsal
tags:
  - leetcode
  - 刷题
  - easy
categories:
  - 刷题
date: 2018-04-05 09:18:00
---
### [448\. Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/description/)

Difficulty: **Easy**



Given an array of integers where 1 ≤ a[i] ≤ _n_ (_n_ = size of array), some elements appear twice and others appear once.

Find all the elements of [1, _n_] inclusive that do not appear in this array.

Could you do it without extra space and in O(_n_) runtime? You may assume the returned list does not count as extra space.
<!--more-->
**Example:**

```
**Input:**
[4,3,2,7,8,2,3,1]

**Output:**
[5,6]
```



#### Solution
```
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var findDisappearedNumbers = function(nums) {
    for (var i = 0; i < nums.length; i++) {
        var value = nums[i];
        var pos = Math.abs(value) - 1;
        nums[pos] = -Math.abs(nums[pos]);
    }
    var ans = [];
    for (var i = 0; i < nums.length; i++) {
        if (nums[i] > 0) {
            ans.push(i + 1);
        } else {
            nums[i] = -nums[i];
        }
    }
    return ans;
}
```
思路：利用下标与内容的对应关系，将数组内的某个元素-1之后对应的元素置为负数，最后看哪几个位置的元素不为负数即可。  
另外，js的数组添加元素是push(),绝对值是Math.abs()