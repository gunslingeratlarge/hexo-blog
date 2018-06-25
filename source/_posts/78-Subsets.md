title: 78. Subsets
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
categories:
  - 刷题
date: 2018-06-11 19:32:00
---
### [78\. Subsets](https://leetcode.com/problems/subsets/description/)

Difficulty: **Medium**



Given a set of **distinct** integers, _nums_, return all possible subsets (the power set).

**Note:** The solution set must not contain duplicate subsets.

**Example:**

```
**Input:** nums = [1,2,3]
**Output:**
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]```

<!--more-->

#### Solution
```
//另外一种解决的思路是不使用回溯（递归)，而是用位的方式来模拟，化递归树结构为线性结构，这样就可以在循环中使用了
//比如说你的nums中有三个数，1,2,3，那么你可以从0 - 111 这样的方式来模拟取了的情况，即从0到2^n - 1,n为nums.size().然后你把每一个三位数
//比如101,都拿出来，并生成对应的集合，并放到res中去就可以了。 但是有一个关键的问题是，你如何把101对应到nums中的第一个数和第三个数。这就需要位运算了。
//没有办法一下子运算出来的，需要从右往左运算。与001进行按位与，然后与010按位与，然后与100按位与。所以还要有一个循环。
​
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        ArrayList<Integer> item = null;
        List<List<Integer>> res = new ArrayList<>();
        //i从0到7
        for (int i = 0; i <= Math.pow(2,nums.length) - 1; i++) {
        
            item = new ArrayList<Integer>();
            int k = 0;
​
            
            for (int j = 1;j <= Math.pow(2, nums.length - 1);j = j << 1, k++) {
                if ((i & j)!= 0) {
                    item.add(nums[k]);
                }
​
            }
            List<Integer> itemclone = (List<Integer>)item.clone();
            res.add(itemclone);
            
        
        }
        return res;
    }
}
```
采用递归进行回溯
``` java
//结合回溯法进行思考。 
//用循环写困难的地方在于你没办法分叉，就是你没办法考虑两种情况，考虑选和不选两种情况，就是你只能一直执行，也没办法说我选了，我可以回到这个位置重新不选。
//如果只生成[1],[2],[3],困难吗？  就很简单，因为你不需要回溯，回到这个做决定的地方，然后不选1，因为所有的情况都是选择了1的。就不是一个树形结构，而是一个线性结构
//用循环的方法写是简单的，但是能不能用递归的方法来只生成[1][1,2][1,2,3]?
//可以试探完一个再试探另一个，比如可以选择了1然后后续的元素继续选择， 然后又可以回溯到对1做选择的位置，因为递归的每一层都保留了当前层的状态，所以你可以利用递归回到当前的状态然后做不一样的选择。 
//list里面嵌套添加list，要注意只是添加了引用进去，而不是添加整个数组的元素进去。引用的话如果被引用数组内容改变了你就尴尬了。
        
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        List<Integer> item = new ArrayList<>();
        backtrack(0,nums,(ArrayList<Integer>)item,(ArrayList<List<Integer>>)res);
        res.add(new ArrayList<Integer>());
        return res;
    }
    
    public void backtrack (int i, int[] nums, ArrayList<Integer> item, ArrayList<List<Integer>> res) {
        if (i >= nums.length) {
            return;
        }
        
        //选择当前元素
        item.add(nums[i]);
        ArrayList<Integer> temp = (ArrayList<Integer>)item.clone();
        res.add(temp);
        //进行下一个元素的选与不选
        backtrack(i + 1, nums,item,res);
        //去掉当前元素
        item.remove(item.size() - 1);
        //进行下一个元素的选与不选
        backtrack(i + 1, nums,item,res);
    }
}
```
