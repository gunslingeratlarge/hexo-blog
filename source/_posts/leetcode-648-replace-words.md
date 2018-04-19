title: leetcode 648 replace words
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
categories:
  - 刷题
date: 2018-04-05 10:02:00
---
### [648\. Replace Words](https://leetcode.com/problems/replace-words/description/)

Difficulty: **Medium**



In English, we have a concept called `root`, which can be followed by some other words to form another longer word - let's call this word `successor`. For example, the root `an`, followed by `other`, which can form another word `another`.

Now, given a dictionary consisting of many roots and a sentence. You need to replace all the `successor` in the sentence with the `root` forming it. If a `successor` has many `roots` can form it, replace it with the root with the shortest length.

You need to output the sentence after the replacement.
<!--more-->
**Example 1:**  

```
**Input:** dict = ["cat", "bat", "rat"]
sentence = "the cattle was rattled by the battery"
**Output:** "the cat was rat by the bat"
```

**Note:**  

1.  The input will only have lower-case letters.
2.  1 <= dict words number <= 1000
3.  1 <= sentence words number <= 1000
4.  1 <= root length <= 100
5.  1 <= sentence words length <= 1000



#### Solution
```
class Solution {
    public String replaceWords(List<String> dict, String sentence) {
        System.out.println("\\b(" + String.join("|",dict)+")*?\\b");
        return sentence.replaceAll("\\b(" + String.join("|",dict)+"){1}.*?\\b","$1");
    }
}
```
思路：使用正则或者使用tier tree，tier tree还不会，这里复习一下正则：  
\* ? + 是量词，是用来修饰一个东西的，注意\*不是通配符里的表示任意多个字符，不要搞混了，表示任意一个字符用.,字母用\w。常用.\*?来表示任意字符的非贪婪匹配。