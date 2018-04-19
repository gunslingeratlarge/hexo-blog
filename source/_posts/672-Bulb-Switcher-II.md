title: leetcode 672. Bulb Switcher II
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
  - ''
categories:
  - 刷题
date: 2018-04-08 09:01:00
---
### [672\. Bulb Switcher II](https://leetcode.com/problems/bulb-switcher-ii/description/)

Difficulty: **Medium**



There is a room with `n` lights which are turned on initially and 4 buttons on the wall. After performing exactly `m` unknown operations towards buttons, you need to return how many different kinds of status of the `n` lights could be.
<!--more-->
Suppose `n` lights are labeled as number [1, 2, 3 ..., n], function of these 4 buttons are given below:

1.  Flip all the lights.
2.  Flip lights with even numbers.
3.  Flip lights with odd numbers.
4.  Flip lights with (3k + 1) numbers, k = 0, 1, 2, ...

**Example 1:**  

```
Input: n = 1, m = 1.
Output: 2
Explanation: Status can be: [on], [off]
```

**Example 2:**  

```
Input: n = 2, m = 1.
Output: 3
Explanation: Status can be: [on, off], [off, on], [off, off]
```

**Example 3:**  

```
Input: n = 3, m = 1.
Output: 4
Explanation: Status can be: [off, on, off], [on, off, on], [off, off, off], [off, on, on].
```

**Note:** `n` and `m` both fit in range [0, 1000].



#### Solution
```
/**
 * @param {number} n
 * @param {number} m
 * @return {number}
 */
var flipLights = function(n, m) {
    if (m == 0 || n == 0) {
        return 1;
    }
    if(n == 1) {
        return 2;
    }
    if ( n ==2 ){
        return m == 1?3:4;
    }
    if (m == 1){
        return 4;
    }
    if (m == 2) {
        return 7;
    }
    return 8;
};
```
分析：这道题有点像寻找规律的问题，只要知道前三个灯泡的状态就可以推出所有灯泡的状态了。总共有八种状态：全开，1,2,3,4，1+4,2+4,3+4（因为1+2 = 3，,2+ 3 = 1， 1+3 = 2）

| 操作数\\灯泡数 |0| 1 | 2 | 3 | 4 | 5 | .. |  
| - | - | - | - | - | - | - |-| 
| 0 |1| 1 | 1 | 1 | 1 | 1 | 1 | 
| 1 |1| 2 | 3 | 4 | 4 | 4 | 4 | 
| 2 |1| 2 | 4 | 7 | 7 | 7 | 7 | 
| 3 |1| 2 | 4 | 8 | 8 | 8 | 8 | 
| 3 |1| 2 | 4 | 8 | 8 | 8 | 8 |