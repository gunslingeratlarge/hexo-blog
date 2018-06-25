title: Polynomial Regression Cheatsheet
author: gsal
tags:
  - python
  - 机器学习
  - ''
categories: []
date: 2018-06-06 13:59:00
---
就是把原来的一个x的特征矩阵扩充为x和x的2次，3次...有一个参数degree就是用来选次数的  
这个回归的方式PolynomialFeatures会自动帮你加上一列1在前面，你就不用自己搞了

``` python
#importing libraries
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt


#loading data
dataset = pd.read_csv("Position_Salaries.csv")
X = dataset.iloc[:,1:2].values
y = dataset.iloc[:,-1].values

#adding polynomial features
from sklearn.preprocessing import PolynomialFeatures
reg_poly = PolynomialFeatures(degree = 4)
X_poly  = reg_poly.fit_transform(X)

#simple linear regression
from sklearn.linear_model import LinearRegression
lin_reg = LinearRegression()
lin_reg = lin_reg.fit(X,y)

#plot simple linear regression
plt.scatter(X,y,color="red")
plt.title("predict salary by level")
plt.xlabel("level")
plt.ylabel("salary")
plt.plot(X, lin_reg.predict(X),c="b")
plt.show()

#polynomial linear regression
poly_lin_reg = LinearRegression()
poly_lin_reg.fit(X_poly,y)

#plot polynomial linear regression

X_grid = np.arange(min(X),max(X),0.1)
X_grid = np.reshape(X_grid,(len(X_grid),1))
plt.scatter(X,y,color="red")
plt.title("predict salary by level(polynomial regression)")
plt.xlabel("level")
plt.ylabel("salary")
plt.plot(X_grid, poly_lin_reg.predict(reg_poly.fit_transform(X_grid)),c="b")
plt.show()

```