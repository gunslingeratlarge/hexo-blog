title: leetcode 2. Add Two Numbers
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
categories:
  - 刷题
date: 2018-04-15 13:17:00
---
### [2\. Add Two Numbers](https://leetcode.com/problems/add-two-numbers/description/)

Difficulty: **Medium**



You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

<!--more-->
**Example**

```
**Input:** (2 -> 4 -> 3) + (5 -> 6 -> 4)
**Output:** 7 -> 0 -> 8
**Explanation:** 342 + 465 = 807.
```



#### Solution
```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode l1cur = l1;
        ListNode l2cur = l2;
        ListNode res = null;
        ListNode rescur = null;
        //l1 != null && l2 != null
        int carry = 0;
        while(l1cur != null || l2cur != null || carry != 0) {
            int value = 0;
            if (l1cur != null && l2cur!=null) {
                value = l1cur.val + l2cur.val + carry;   
                l1cur = l1cur.next;
                l2cur = l2cur.next;
            } else if (l1cur == null && l2cur != null){
                value = l2cur.val + carry;
                l2cur = l2cur.next;
            } else if (l2cur == null && l1cur != null) {
                value = l1cur.val + carry;
                l1cur = l1cur.next;
            } else {
                value = carry;
            }
            carry = 0;
            if (value >= 10) {
                //加法中carry最多为1
                carry = 1;
                value -= 10;
            }
            
            //第一个结点的值
            if (res == null) {
                res = new ListNode(value);
                rescur = res;
            } else {
            //其他节点的值：value 
            ListNode curNode = new ListNode(value);
            rescur.next = curNode;
            rescur = curNode; 
            }
        }
        return res;
    }
}
```
思路与收获：一定要想得比较清楚了再做题，思路清晰做起来很快，debug也容易。不要没有什么思路或者有一点点想法了就开始写，结果写到一半了发现自己想错了又涂涂改改。这道题本身没有什么难度，只要注意进位就可以了，各个情况分类讨论清楚就行了。