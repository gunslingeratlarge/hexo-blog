title: 如何build一个模型
author: gsal
tags:
  - 机器学习
categories:
  - 机器学习
date: 2018-06-03 18:57:00
---
## 步骤
1. All-in
1. 后向消去（Backword Elimination）
1. 正向选择（Forward Selection）
1. 双向选择
1. 分数比较

## 思路
我们怎样去从非常多的feature中挑选出那些有用的特征呢？这个pdf讲的很清楚我们就不多说了。    http://www.superdatascience.com/wp-content/uploads/2017/02/Step-by-step-Blueprints-For-Building-Models.pdf  
这里的P值和significance level（显著性水平）是统计学里的概念，p < sl是显著的，一般sl取0.05。比较快速的挑选feature的方法是backword elimilation。

## Backword Elimination
1. 把所有的feature都扔进去，训练出一个模型
1. 找出这个模型对应的p值最高的特征，如果大于0.05，扔掉，训练一个新的模型重复这个过程，直到没有特征的p值大于0.05