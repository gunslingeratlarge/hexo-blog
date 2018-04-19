title: leetcode 506. Relative Ranks
author: gsal
tags:
  - leetcode
  - 刷题
  - easy
categories:
  - leetcode
date: 2018-04-12 13:43:00
---
### [506\. Relative Ranks](https://leetcode.com/problems/relative-ranks/description/)

Difficulty: **Easy**



Given scores of **N** athletes, find their relative ranks and the people with the top three highest scores, who will be awarded medals: "Gold Medal", "Silver Medal" and "Bronze Medal".

**Example 1:**  
<!--more-->
```
**Input:** [5, 4, 3, 2, 1]
**Output:** ["Gold Medal", "Silver Medal", "Bronze Medal", "4", "5"]
**Explanation:** The first three athletes got the top three highest scores, so they got "Gold Medal", "Silver Medal" and "Bronze Medal".   
For the left two athletes, you just need to output their relative ranks according to their scores.
```

**Note:**  

1.  N is a positive integer and won't exceed 10,000.
2.  All the scores of athletes are guaranteed to be unique.



#### Solution
```
class Solution {
    public String[] findRelativeRanks(int[] nums) {
        int[] descent = Arrays.copyOf(nums,nums.length);
        Arrays.sort(descent);
        for(int start = 0, end = descent.length - 1; start < end; start++,end--){
            int temp = descent[start];
            descent[start] = descent[end];
            descent[end] = temp;
        }
        Map<Integer,Integer> map = new HashMap<>();
        for (int i =0; i < descent.length; i++) {
            map.put(descent[i],i+1);
        }
        String[] res = new String[nums.length];
        for (int i = 0; i < nums.length;i++) {
            if(map.get(nums[i]) == 1) {
                res[i] = "Gold Medal";
            } else if (map.get(nums[i]) == 2) {
                res[i] = "Silver Medal";
            } else if (map.get(nums[i]) == 3) {
                res[i] = "Bronze Medal";
            } else {
                res[i] = "" + map.get(nums[i]);
            }
        }
        return res;
    }
}
```

思路：将得分-名次放到map中，如何找到某个得分的名词呢？ 排序后的index就是名词。另外，java中对数组逆序可以很快地写出来，左、右两个指针，相互交换，左加加，右减减，直到左大于等于右。
int转String：Integer.parseInt(num);
String转int: num + ""  String.valueOf(num);