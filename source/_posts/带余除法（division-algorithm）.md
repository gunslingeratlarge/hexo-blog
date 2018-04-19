title: 复数
author: gsal
tags:
  - 数学
categories:
  - 数学
date: 2018-03-24 17:11:00
---
本笔记是[Complex Numbers Part Imaginary, but Really Simple](https://www.youtube.com/watch?v=Jkv-55ndVYY)的学习笔记。  
#### 复数平面
可以通过复数平面来直观地观察复数，横轴为实轴，代表我们的实数所在的轴，竖轴为虚轴。
![upload successful](/images/complex.png)
复数平面上的每一个点都可以用a + bi来表示，比如A点就是$\frac{-1 + i}{\sqrt2}$,B点就是i，C点就是1。
每一个复数还可以用极坐标表示，即a + bi可以表示为r(cosθ+ isinθ)这里r是长度，$r = \sqrt{a^2 + b^2}$，θ是极坐标的角度。
在极坐标中，两个复数运算，是r相乘，θ相加。（用极坐标乘乘便知） 
#### 欧拉公式
我们将$e^x$展开，有$e^x = 1 + x + \frac{1}{2}x^2 + \frac{1}{6}x^3+...$
令$x = i\theta$, 
$e^{i\theta } = 1 + i\theta + \frac{1}{2}(i\theta )^2 + \frac{1}{6}(i\theta )^3 + ... = 1 - \frac{1}{2}\theta ^2 + i(\theta - \frac{1}{6}\theta^3)+ ... = \cos\theta+ i \sin\theta$
所以就可以把复数表示为$re^{i\theta }$。