title: javascript闭包
author: gsal
tags:
  - 前端
  - js
categories:
  - 前端
date: 2018-05-03 10:35:00
---
### 理解
我对闭包的理解是：一个函数返回另一个函数，另一个函数可以使用这个函数的局部变量。另一个函数可以之后再调用。   
为什么要使用闭包？ 因为在js中没有类，那怎么样制造私有变量和getter、setter的概念呢？  
<!--more-->
可以这样写：
``` js
'use strict';

function create_counter(initial) {
    var x = initial || 0;
    return {
        inc: function () {
            x += 1;
            return x;
        }
    }
}

```
这样使用：
```
var c1 = create_counter();
c1.inc(); // 1
c1.inc(); // 2
c1.inc(); // 3

var c2 = create_counter(10);
c2.inc(); // 11
c2.inc(); // 12
c2.inc(); // 13
```
还可以封装一些方法（给方法预置参数）比如:
``` js
var power_factory = function(n) {
    return function(x) {
        return Math.pow(x,n);
    }
}

var pow2 = power_factory(2);

var pow3 = power_factory(3);

console.log(pow2(6));
console.log(pow3(5));
```

参考:[廖雪峰的博客](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/00143449934543461c9d5dfeeb848f5b72bd012e1113d15000)