title: 46. Permutations
author: gsal
tags:
  - leetcode
categories:
  - leetcode
date: 2018-06-28 12:57:00
---
Given a collection of distinct integers, return all possible permutations.

``` java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> item = new ArrayList<>();
        List<Integer> numset = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            numset.add(nums[i]);
        }
        
        backtrace(res,item,0,numset, nums.length);
        return res;
    }
    
    public void backtrace(List<List<Integer>>res, List<Integer> item, int index, List<Integer> numset,int n) {
        if (index == n) {
            res.add(new ArrayList<Integer>(item));
            return;
        }        
        
        for (int i = 0; i < numset.size(); i++) {
            int num = numset.get(i);
            item.add(num);
            numset.remove(i);
           

           // System.out.println("-----------");
            backtrace(res,item,index + 1,numset,n);
            item.remove(item.size() - 1);
            numset.add(i, num);
        }
        
    }
};
```