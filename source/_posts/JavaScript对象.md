title: JavaScript 模块与继承
author: gsal
tags:
  - 前端
  - Js
categories:
  - 前端
date: 2018-05-04 20:24:00
---
`exports.a = a`或者`exports.add = add`，既可以用来暴露自己的变量也可以用来暴露自己的方法。但是如果说方法是一个构造函数，那么按照这样的方法暴露出来并被引用的话会将该构造方法视为一个普通的方法，并返回undefined。
实际上require方只能看到module.exports，而exports是对module.exports的引用，也就是说，exports.a = a 相当于module.exports.a = a，而且也可以用exports.Student = exports.Student来导出构造方法，用var xiaoming = new Stu.Student("xiaoming");来构造小明，只不过要带上模块名Stu，如果想要不带模块名，直接引出一个类的话，就用module.exports，这样子就能直接看到这个构造方法了。

### 继承
要搞懂继承就必须要先弄明白`prototype`和`__proto__`，知乎上有一个非常浅显易懂的解释，https://www.zhihu.com/question/34183746/answer/58068402