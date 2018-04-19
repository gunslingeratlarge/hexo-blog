title: leetcode 791. Custom Sort String
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
categories:
  - leetcode
date: 2018-04-12 14:36:00
---
### [791\. Custom Sort String](https://leetcode.com/problems/custom-sort-string/description/)

Difficulty: **Medium**



`S` and `T` are strings composed of lowercase letters. In `S`, no letter occurs more than once.

`S` was sorted in some custom order previously. We want to permute the characters of `T` so that they match the order that `S` was sorted. More specifically, if `x` occurs before `y` in `S`, then `x` should occur before `y` in the returned string.

Return any permutation of `T` (as a string) that satisfies this property.
<!--more-->
```
**Example :**
**Input:** 
S = "cba"
T = "abcd"
**Output:** "cbad"
**Explanation:** 
"a", "b", "c" appear in S, so the order of "a", "b", "c" should be "c", "b", and "a". 
Since "d" does not appear in S, it can be at any position in T. "dcba", "cdba", "cbda" are also valid outputs.
```

**Note:**

*   `S` has length at most `26`, and no character is repeated in `S`.
*   `T` has length at most `200`.
*   `S` and `T` consist of lowercase letters only.



#### Solution
```
class Solution {
    public String customSortString(String S, String T) {
        //在s中，则先排了，如果不在s中，最后再排，所以要遍历s
        int[] chs = new int[26];
        for (char c: T.toCharArray()) {
            chs[c - 'a']++;
        }  
        StringBuilder sb = new StringBuilder();
        for (char c:S.toCharArray()) {
            while(chs[c - 'a']-- > 0) {
                sb.append(c);
            }
        }
        
        for (char c: T.toCharArray()) {
            while(chs[c-'a']-- > 0) {
                sb.append(c);
            }
        }
        return sb.toString();
        
    }
}
```
思路：将T中字符放到桶中，每个字母有几个放放好。然后遍历s，在s中的就拿出去先排了，最后遍历t，把t剩下的(不在s中的）再放进去。