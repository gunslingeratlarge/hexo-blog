title: Support Vector Regression Cheatsheet
author: gsal
tags:
  - 机器学习
  - python
categories:
  - 机器学习
date: 2018-06-06 15:51:00
---
## 概念
svr本身不像sklearn的线性模型会帮你做feature_scaling，所以需要你自己进行放缩，在fit完regressor之后预测的时候要对输入（X）进行放缩，最后出来之后要对y进行放缩。即要预测要先进行特征放缩，然后得到了预测值之后还要放缩回去。可以通过array.reshape(-1,1)将一个向量转成一个一列的矩阵。通过dataframe.values将DataFrame转成ndarray。可以通过`np.array([[1,2,3],[2,3,4]])`构建一个2×3的矩阵。


## 代码
``` python
# -*- coding: utf-8 -*-
# SVR

# Importing the libraries
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

# Importing the dataset
dataset = pd.read_csv('Position_Salaries.csv')
X = dataset.iloc[:, 1:2].values
y = dataset.iloc[:, 2].values

# Splitting the dataset into the Training set and Test set
"""from sklearn.cross_validation import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)"""

# Feature Scaling
from sklearn.preprocessing import StandardScaler
sc_X = StandardScaler()
sc_y = StandardScaler()
X_scaled = sc_X.fit_transform(X)
#turn y into an array
y_scaled = sc_y.fit_transform(y.reshape(-1,1))
y_scaled = y_scaled[:,-1]

#after scaling, fit to svr model
from sklearn.svm import SVR
regressor = SVR(kernel="rbf")
regressor.fit(X_scaled,y_scaled)


X_grid = np.arange(min(X_scaled),max(X_scaled),0.1)
X_grid = X_grid.reshape(-1,1)
plt.scatter(X_scaled,y_scaled,c="r")
plt.plot(X_grid, regressor.predict(X_grid),c ="b")
plt.show()

#to predict value, we need to reverse the scale process
#y_predict = sc_y.inverse_transform(sc_X.transform(regressor.predict(np.array(6.5).reshape(-1,1))))
#要预测要先进行特征放缩，然后得到了预测值之后还要放缩回去
xx = np.array([[6.5]])
xx = sc_X.transform(xx)
yy = regressor.predict(xx)
yy.reshape(-1,1)
yy = sc_y.inverse_transform(yy)
```