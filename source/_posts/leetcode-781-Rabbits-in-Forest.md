title: leetcode 781. Rabbits in Forest
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
  - ''
categories:
  - 刷题
date: 2018-04-10 12:31:00
---
### [781\. Rabbits in Forest](https://leetcode.com/problems/rabbits-in-forest/description/)

Difficulty: **Medium**



In a forest, each rabbit has some color. Some subset of rabbits (possibly all of them) tell you how many other rabbits have the same color as them. Those `answers` are placed in an array.

Return the minimum number of rabbits that could be in the forest.
<!--more-->
```
**Examples:**
**Input:** answers = [1, 1, 2]
**Output:** 5
**Explanation:**
The two rabbits that answered "1" could both be the same color, say red.
The rabbit than answered "2" can't be red or the answers would be inconsistent.
Say the rabbit that answered "2" was blue.
Then there should be 2 other blue rabbits in the forest that didn't answer into the array.
The smallest possible number of rabbits in the forest is therefore 5: 3 that answered plus 2 that didn't.

**Input:** answers = [10, 10, 10]
**Output:** 11

**Input:** answers = []
**Output:** 0
```

**Note:**

1.  `answers` will have length at most `1000`.
2.  Each `answers[i]` will be an integer in the range `[0, 999]`.



#### Solution
```
class Solution {
    public int numRabbits(int[] answers) {
        Arrays.sort(answers);
        int capacity = 0;
        int res = 0;
        int count = 0;
        for (int i = 0; i < answers.length;i++) {
            //对开头单独处理
            if (i == 0) {
                capacity = answers[i] + 1;
                count = 1;
                if (capacity == 1) {
                    count = 0;
                    res += capacity;
                }
            } else {
                //如果后一个跟前一个相同
                if (answers[i - 1] == answers[i]) {
                    count++;
                    if (count == capacity) {
                        res += capacity;
                        count = 0;
                    }
                    if (count != 0 && i == (answers.length - 1)) {
                        res += capacity;
                    }
                    //如果后一个跟前一个不同
                } else {
                    //如果前一个回答还有兔子
                    if (count != 0) {
                        res += capacity;
                        count = 0;
                    }
                    capacity = answers[i] + 1;
                    //如果是最后一个回答
                    if (i == answers.length - 1) {
                        res += capacity;
                    }
                    count++;
                }
            }
        }
        return res;
    }
}
```
思路：将兔子们的回答排序，然后遍历数组，设置一个capacity来表示这个回答能够装下多少只同样颜色的兔子，比如回答为2，那么就至少有3只同样颜色的兔子，如果说回答是2的兔子超过了3（count > 3），那么就要另外加上一个capacity了。此外，对于末尾的兔子和分界处的兔子也要额外考虑。  
要想清楚再写代码，不要没想清楚就开始写，实际上写出来的代码一团糟。