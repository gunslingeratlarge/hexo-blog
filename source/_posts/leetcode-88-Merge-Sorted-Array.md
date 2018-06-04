title: leetcode 88. Merge Sorted Array
author: gsal
tags:
  - leetcode
  - easy
  - 刷题
categories:
  - 刷题
date: 2018-04-20 09:31:00
---
### [88\. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/description/)

Difficulty: **Easy**



Given two sorted integer arrays _nums1_ and _nums2_, merge _nums2_ into _nums1_ as one sorted array.

**Note:**

*   The number of elements initialized in _nums1_ and _nums2_ are _m_ and _n_ respectively.
*   You may assume that _nums1_ has enough space (size that is greater or equal to _m_ + _n_) to hold additional elements from _nums2_.

<!--more-->
**Example:**

```
**Input:**
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

**Output:** [1,2,2,3,5,6]
```



#### Solution
```
//从后往前
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        //nums1的最后一位
        int i = m - 1;
        //nums2的最后一位
        int j = n - 1;
        //总数组的最后一位
        int k = m + n - 1;
        
        while(i >= 0 && j >= 0) {
            if (nums1[i] > nums2[j]) {
                nums1[k--] = nums1[i--];
            } else {
            nums1[k--] = nums2[j--];
            }
        } 
        
        while(j >= 0) {
            nums1[k--] = nums2[j--];
        }
    }
}
```