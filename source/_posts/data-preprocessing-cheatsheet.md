title: Data Preprocessing Cheatsheet
author: gsal
tags:
  - python
  - 机器学习
categories:
  - 机器学习
date: 2018-06-04 21:28:00
---
## 数据预处理流程
首先是读取数据，数据读完了之后有缺失数据，那肯定要把缺失数据补充完整。数据补充完整之后对于categorical的变量要将它转成numerical的或者是one-hot编码（dummy variables）。这样我们的数据看起来就比较舒服。  
接下来就要划分好自变量和因变量，并且划分训练集和测试集，那么最后再做一下feature scaling，将这些特征都放缩到差不多的范围里来。
<!--more-->
## 完整的预处理代码
数据的预处理可以有很多种方式，这里给出的只是一种，并且对于复杂的情况可能也无法处理。就需要在此基础上不断补充，举一反三了。

``` python
# -*- coding: utf-8 -*-
"""
Created on Sat Jun  2 19:44:53 2018

@author: Gunslinger
"""


#import libraries
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

#load data
dataset = pd.read_csv("Data.csv")
X = dataset.iloc[:,:-1].values
Y = dataset.iloc[:,3].values

#import Imputer to fill the missing data
from sklearn.preprocessing import Imputer
imputer = Imputer(missing_values="NaN",strategy="mean",axis=0)
imputer = imputer.fit(X[:,1:3])
X[:,1:3] = imputer.transform(X[:,1:3])  


#Encoding categorical data 
from sklearn.preprocessing import LabelEncoder
labelencoder_x =  LabelEncoder()
X[:,0] = labelencoder_x.fit_transform(X[:,0])

labelencoder_y = LabelEncoder()
Y =labelencoder_y.fit_transform(Y)


#onehot encode
from sklearn.preprocessing import OneHotEncoder
onehotencoder = OneHotEncoder(categorical_features=[0])
X = onehotencoder.fit_transform(X).toarray()

#train-test-splitting
from sklearn.cross_validation import train_test_split
X_train, X_test, Y_train, Y_test = train_test_split(X,Y,test_size=0.2, random_state = 0)


#Feture Scaling
from sklearn.preprocessing import StandardScaler
sc_X = StandardScaler()
#FIT means to compute nessecary numbers and variables to modify the X
X_train = sc_X.fit_transform(X_train)
X_test = sc_X.transform(X_test)

```
## 其他
这里提一下其他的一些收获和知识点。
1. 下载anaconda然后无脑安装，打开spyder就可以直接开始写代码，不用下载各种乱七八糟的库了，都已经给你准备好了。
1. ctrl + I 可以调出某个方法的帮助文档，非常有用
1. ctrl + enter 执行选中的代码段
1. 注意到`X = dataset.iloc[:,:-1].values`与`Y = dataset.iloc[:,3].values`的区别。如果直接是`[:,3]`那只会得到一个数组，是一维的。而如果`[:,3:4]`还是拿的一列，但是会得到一个一列的matrix。这是一个比较微妙的区别
1. onehot编码在训练模型的时候一定要省略掉一位。因为不省略掉信息就冗余了。
1. fit和transform的意思是将这个工具（encoder，imputer之类的）根据你输入的矩阵、数组进行操作，然后再转换形式。也说不清，反正都是一起用的好像。