title: leetcode 189. Rotate Array
author: gsal
tags:
  - leetcode
  - easy
  - 刷题
categories:
  - 刷题
date: 2018-04-17 13:44:00
---
### [189\. Rotate Array](https://leetcode.com/problems/rotate-array/description/)

Difficulty: **Easy**



Rotate an array of _n_ elements to the right by _k_ steps.

For example, with _n_ = 7 and _k_ = 3, the array `[1,2,3,4,5,6,7]` is rotated to `[5,6,7,1,2,3,4]`.

**Note:**  
Try to come up as many solutions as you can, there are at least 3 different ways to solve this problem.

**Hint:**  
Could you do it in-place with O(1) extra space?

Related problem:



#### Solution
```
class Solution {
    public void rotate(int[] nums, int k) {
        int[] pos = new int[nums.length];
        k = k % nums.length;
        for (int i = 0; i < nums.length; i++) {
            pos[i] = (i + nums.length - k)%nums.length;
        }
        int[] temp = Arrays.copyOf(nums,nums.length);
         
        for (int i = 0; i < nums.length; i++) {
            nums[i] = temp[pos[i]];
        }
    }
}
```