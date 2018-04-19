title: leetcode 3. Longest Substring Without Repeating Characters
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
categories:
  - 刷题
date: 2018-04-17 12:53:00
---
### [3\. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)

Difficulty: **Medium**



Given a string, find the length of the **longest substring** without repeating characters.

**Examples:**

Given `"abcabcbb"`, the answer is `"abc"`, which the length is 3.

Given `"bbbbb"`, the answer is `"b"`, with the length of 1.

Given `"pwwkew"`, the answer is `"wke"`, with the length of 3\. Note that the answer must be a **substring**, `"pwke"` is a _subsequence_ and not a substring.

<!--more-->


#### Solution1
```
class Solution {
    public int lengthOfLongestSubstring(String s) {
        //做法：从重复元素的下一个元素开始考察
        char[] chs = s.toCharArray();
        
        //只能处理26个小写字母，不能处理特殊符号，因此还是要使用map
        //int[] pos = new int[26];
        Map<Character,Integer> map = new HashMap<>();
        //Arrays.fill(pos,-1);
        int head = 0, tail = 0, res = 0, count = 0;
        while(tail < chs.length) {
            if (!map.containsKey(chs[tail]))  {
                //pos[chs[tail] - 'a'] = tail;
                map.put(chs[tail],tail);
                count++;
            } else {
                
                //head不应该等于tail，而应该等于两个重复元素中的第一个的下一个
                //head = pos[chs[tail] - 'a'] + 1;
                head = map.get(chs[tail]) + 1;
                
                //arr全部置为false
                //Arrays.fill(pos,-1);
                map.clear();
                //从head到tail，初始化其中的元素对应在arr中的值
                for (int i = head; i <= tail; i++) {
                    map.put(chs[i],i);
                }
                
                res = Math.max(count, res);
                count = tail - head + 1;
                
            }
            tail++;
        }
        //如果最后一个元素仍然满足条件的话，res不会被更新
        return Math.max(count,res);
    }
}
```

#### Solution2
``` java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int head,tail,max = 0;
        Map<Character, Integer> map = new HashMap<>();
        for (head = 0, tail = 0; tail < s.length(); tail++ ) {
            if (map.containsKey(s.charAt(tail))) {
                head = Math.max(head, map.get(s.charAt(tail)) + 1);
            }
            map.put(s.charAt(tail),tail);
            max = Math.max(max, tail - head + 1);
        }
        return max;
    }
}
```

#### 思路
都是从重复元素的下一个元素开始考察，都使用了hashmap来记录字符的位置，第二种写法有两个特点：
1. max我不再等到产生最大值的情况再求，我每次都求，那么只要你有最大值产生，那么一定跑不掉，这样就减少了什么时候产生max情况的判断
1. 当head去到了字符串的中间，怎么去判断某个字符有没有在这个子串中，我第一种做法是每次更新了head的值就重新初始化map，把子串的字符位置重新放进去，效率很低，第二种做法是不重新初始化，而是判断重复的字符的位置是不是在我head之后，也就是是不是我子串的一部分，如果是我就更新head，不是我就不管了。非常巧妙。