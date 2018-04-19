title: leetcode 55.Jump game
author: gsal
tags:
  - 刷题
  - leetcode
  - medium
categories:
  - leetcode
date: 2018-04-11 09:16:00
---
### [55\. Jump Game](https://leetcode.com/problems/jump-game/description/)

Difficulty: **Medium**

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

For example:  
A = `[2,3,1,1,4]`, return `true`.

A = `[3,2,1,0,4]`, return `false`.
<!--more-->



#### Solution
```
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var canJump = function(nums) {
    var reach = 0;
    for (var i = 0; i <= reach && i < nums.length; i++) {
        reach = Math.max(reach, nums[i] + i);
    }
    if (reach >= (nums.length - 1)) {
        return true;
    } else {
        return false;
    }
};
```
用reach来表示能够最大走到的边界，如果说最大能够走到的边界比最右的index大，说明可以达到，否则不行。
我一开始使用递归做，即递归判断某个位置出发能够走到的结点能不能到达终点，其实也可以，不过最后超时了，如果结合dp来进行递归的话我觉得是可以的，回头有时间再重新做一做。