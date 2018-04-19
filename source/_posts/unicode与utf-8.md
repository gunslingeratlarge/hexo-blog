title: unicode与utf-8
author: gsal
tags:
  - 其他
categories:
  - 其他
date: 2018-04-11 09:06:00
---
参考 https://blog.csdn.net/bluetjs/article/details/52936943
#### unicode
unicode只是一个符号集，只是规定了每个符号的二进制表示，而没有规定说应该怎样在计算机内存储。
#### utf-8
是unicode的编码方式，是变长的编码，对于一个字节就可以表示的英文字母，我们就用一个字节，而汉字就用3或者4个字节表示。
<!--more-->
``` java
String a = "abc中国ren";
    for(byte b: a.getBytes("utf-8")) {
        System.out.println(b);
    }
    System.out.println(a.getBytes("utf-8").length);
```
输出为：97 98 99 -28 -72 -83 -27 -101 -67 114 101 110 12  
即`abc中国ren`这个字符串在内存中存为12个字节，每个英文字母一个字节，中文三个字节。  
那它是怎样编码的呢？  
> UTF-8的编码规则很简单，只有二条：
1）对于单字节的符号，字节的第一位设为0，后面7位为这个符号的unicode码。因此对于英语字母，UTF-8编码和ASCII码是相同的。
2）对于n字节的符号（n>1），第一个字节的前n位都设为1，第n+1位设为0，后面字节的前两位一律设为10。剩下的没有提及的二进制位，全部为这个符号的unicode码。

这样就可以将ascii码和汉字的二进制表示分开来了。