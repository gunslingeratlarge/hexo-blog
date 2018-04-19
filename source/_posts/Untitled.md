title: KMP
author: gsal
tags:
  - 数据结构
  - 算法
categories:
  - 算法
date: 2018-04-04 13:42:00
---
https://www.youtube.com/watch?v=GTJr8OvyEVQ
#### 介绍  
目标串：abcxabcdabxabcdabcdabcy  
模式串：abcdabcy  
想要找到模式串是否是目标串的子串，如果是，在目标串的index是多少  
<!--more-->
#### 思路
* 既是前缀又是后缀：如abcab，这里的ab就既是前缀又是后缀  

按照暴力求解的办法从头开始比较。比较到不匹配的位置时，观察模式串不匹配位置之前是否有即是前缀又是后缀的一个子串   
比如：从头开始，abc都匹配，目标的x和模式的d不匹配 ，则观察模式串的d之前的子串abc是否有即是前缀又是后缀的一部分。没有既是前缀又是后缀，所以从不匹配位置再从头开始，即模式串的开头a与目标串的x开始比较。即abc**x**abcdabxabcdabcdabcy与**a**bcdabcy比较。可以一直匹配到abcx**abcdab**xabcdabcdabcy，**abcdab**cy，此时x与c比较不匹配。我们就看abcdab这个已经匹配了的子串，它是否有既是前缀又是后缀的一部分。有的，对吗？ab既是它的前缀，又是它的后缀。  
现在我们看这两个字符串：  
abcx**abcd*ab***xabcdabcdabcy  
**abcd*ab***cy
注意到由于加粗的部分是互相匹配的，因此斜体的两部分肯定是匹配的，对吧。   
又由于加粗的部分中ab既是后缀又是前缀，也就是说模式串的前两个ab和目标串的斜体ab肯定是匹配的，因此我们就可以不再从目标串的x与模式串的第一个a开始比较了，而是比较x与模式串的第三个字母c，但是x与c不匹配，所以又匹配目标串x之后的a和模式串的开头a。  
最后比较到这里，  
目标串：abcxabcdabx**abcdabc**dabcy  
模式串：**abcdabc**y  
d与y不匹配，但是abcdabc满足abc既是前缀又是后缀，因此从模式串的index = 3的d开始与目标串的下一个d进行比较，一直到结束。  
这就是kmp的一个思路。  

#### 求模式串的next数组
next数组就是表示pattern串中既是前缀又是后缀的字符位数的：  
如abcdabcy这个模式串  
它的next数组为0 0 0 0 1 2 3 0  
##### 应该怎么求呢？
说再多不如看视频
https://www.youtube.com/watch?v=KG44VoDtsAA  

##### 为什么要这样求呢？
关键点在于，如果i和j所指的字符不同，为什么要将j移到其前一位的next数组值的位置。一图胜千言：
![kmp求next数组](/images/kmpnextarray.png)

#### 应用next数组
![kmp的过程](/images/kmp.png)
#### 代码实现
待续