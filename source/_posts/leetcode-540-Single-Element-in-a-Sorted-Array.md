title: leetcode 540. Single Element in a Sorted Array
author: gsal
tags:
  - leetcdde
  - 刷题
  - meduim
categories:
  - 刷题
date: 2018-04-16 20:16:00
---
### [540\. Single Element in a Sorted Array](https://leetcode.com/problems/single-element-in-a-sorted-array/description/)

Difficulty: **Medium**



Given a sorted array consisting of only integers where every element appears twice except for one element which appears once. Find this single element that appears only once.
<!--more-->
**Example 1:**  

```
**Input:** [1,1,2,3,3,4,4,8,8]
**Output:** 2
```

**Example 2:**  

```
**Input:** [3,3,7,7,10,11,11]
**Output:** 10
```

**Note:** Your solution should run in O(log n) time and O(1) space.



#### Solution
```
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int res = 0;
        for (int num : nums) {
            res ^= num;
        }
        return res;
    }
}
```
思路：按位异或，一样的就变为0了，留下来的就是那个single number