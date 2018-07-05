title: leetcode 70. Climbing Stairs 老题新做
author: gsal
tags:
  - leetcode
categories:
  - 刷题
date: 2018-07-01 18:58:00
---
# leetcode 70. Climbing Stairs
@(Input)[leetcode]

Difficulty: **Easy**



You are climbing a stair case. It takes _n_ steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Note:** Given _n_ will be a positive integer.
<!--more-->

**Example 1:**

```
**Input:** 2
**Output:** 2
**Explanation:** There are two ways to climb to the top.
1\. 1 step + 1 step
2\. 2 steps
```

**Example 2:**

```
**Input:** 3
**Output:** 3
**Explanation:** There are three ways to climb to the top.
1\. 1 step + 1 step + 1 step
2\. 1 step + 2 steps
3\. 2 steps + 1 step
```



#### Solution
```
class Solution {
    public int climbStairs(int n) {
        int[] dp = new int[n + 1];
        return dpClimb(n,dp);
        
    }
    
    public int dpClimb(int n, int[] dp) {
        if (n == 1) {
            dp[1] = 1;
            return 1;
        } 
        if (n == 2) {
            dp[2] = 2;
            return 2;
        } 
        
        if (dp[n] == 0) {
            int temp = dpClimb(n-1,dp) + dpClimb(n-2,dp);
            dp[n] = temp;
            return temp;
        } else {
            return  dp[n];
        }
    }
}
```

这道题的思路是有个演变的过程的，一开始只是采用递归，把n个台阶的走法 = n - 1个台阶走法 + n - 2个台阶走法，但是会超时，这时候我们考虑使用dp[n + 1]记录所有的走法数，这样在之后再次遇到某一个走法的时候我们就不用重复递归计算了。因此有了上面的solution。但是这涉及到递归函数调用，还可以用循环进行改进。就是我们可以直接提前将整个dp数组都计算出来，而不是等到要算某个n的时候再计算。因此有了第二种solution，见下：
``` java
class Solution {
    public int climbStairs(int n) {
        int[] dp = new int[n + 1];

        for (int i = 1; i < n + 1; i++) {
            if (i == 1 || i == 2) {
                dp[i] = i;
            } else {
                dp[i] = dp[i - 1] + dp[i - 2];
            }
        }
        return dp[n];
    }
}
```
思路是预先算出整个dp数组，需要哪个取哪个。简单直观。因为一个dp[n]是可以由dp[n - 1]和dp[n - 2]完全确定的。所以可以这样做。