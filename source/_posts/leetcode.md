title: leetcode 717. 1-bit and 2-bit Characters
author: gsal
tags:
  - leetcode
  - 刷题
  - easy
categories:
  - 刷题
date: 2018-04-03 19:04:00
---
### [717\. 1-bit and 2-bit Characters](https://leetcode.com/problems/1-bit-and-2-bit-characters/description/)

Difficulty: **Easy**



We have two special characters. The first character can be represented by one bit `0`. The second character can be represented by two bits (`10` or `11`).

Now given a string represented by several bits. Return whether the last character must be a one-bit character or not. The given string will always end with a zero.
<!--more-->
**Example 1:**  

```
Input:
bits = [1, 0, 0]
Output: True
Explanation: 
The only way to decode it is two-bit character and one-bit character. So the last character is one-bit character.
```

**Example 2:**  

```
Input: 
bits = [1, 1, 1, 0]
Output: False
Explanation:
The only way to decode it is two-bit character and two-bit character. So the last character is NOT one-bit character.
```

**Note:**

*   `1 <= len(bits) <= 1000`.
*   `bits[i]` is always `0` or `1`.

#### Solution
```
/**
 * @param {number[]} bits
 * @return {boolean}
 */
var isOneBitCharacter = function(bits) {
    if (bits.length === 0) {
        return false;
    }
    
    var len = bits.length;
    if (len === 1) {
        return true;
    }
    var secondzero;//倒数第二个零
    for (var i = len - 2; i >= 0; i--) {
        if (bits[i] === 0) {
            secondzero = i;
            break;
        }
    }
    if (secondzero == undefined) {
        if (len % 2 == 0) {
            return false;
        } else {
            return true;
        }
    } else {
        if ((len - secondzero) % 2 == 0) {
            return true;
        } else {
            return false;
        }
    }
};
```

注意几点：  
1. 求数组长度用数组名.length
1. 涉及到成对出现、单个出现，应联想到奇偶性