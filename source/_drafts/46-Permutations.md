{}
date: 2018-06-28 12:52:38
---
### [46\. Permutations](https://leetcode.com/problems/permutations/description/)

Difficulty: **Medium**



Given a collection of **distinct** integers, return all possible permutations.

**Example:**

```
**Input:** [1,2,3]
**Output:**
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```



#### Solution
```
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
就是通常的回溯法，通过循环遍历所有还剩下的数字，每当调用backtrace函数的时候就是进入选择下一个