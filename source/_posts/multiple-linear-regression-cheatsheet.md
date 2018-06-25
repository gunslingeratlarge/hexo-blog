title: Multiple Linear Regression Cheatsheet
author: gsal
tags:
  - python
  - 机器学习
categories:
  - 机器学习
date: 2018-06-06 10:20:00
---
## 多元线性回归
线性不是用来讲x的，是用来讲系数的。
需要注意要省略掉dummy variable中的一位来避免dummy variable trap。  
import matplotlib.pyplot as plt
## 代码
``` python
# -*- coding: utf-8 -*-
"""
Created on Tue Jun  5 16:45:51 2018

@author: Gunslinger
"""

#import libraries
import numpy as np
import pandas as pd

#readfile -pd
dataset = pd.read_csv("50_Startups.csv")
X = dataset.iloc[:,:-1].values
y = dataset.iloc[:,-1].values


#one hot encode
from sklearn.preprocessing import LabelEncoder,OneHotEncoder
labelencoder = LabelEncoder()
X[:,-1] =  labelencoder.fit_transform(X[:,-1])
onehotencoder = OneHotEncoder(categorical_features=[3])
X = onehotencoder.fit_transform(X).toarray()

#avoid dummy variable trap
X = X[:,1:]

# train test split
from sklearn.cross_validation import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X,y,test_size=0.2,random_state = 44)

#fit model (adding ones before the first column to address the const value)
import statsmodels.formula.api as sm
X = np.append(np.ones((50,1)).astype(int),X,axis=1)
X_opt = X[:,[0,1,2,3,4,5]]
regressor_OLS = sm.OLS(endog = y, exog=X_opt).fit()
regressor_OLS.summary()

#delete x2:dummy 2
X_opt = X[:,[0,1,3,4,5]]
regressor_OLS = sm.OLS(endog = y, exog=X_opt).fit()
regressor_OLS.summary()

#delete x1
X_opt = X[:,[0,3,4,5]]
regressor_OLS = sm.OLS(endog = y, exog=X_opt).fit()
regressor_OLS.summary()

#delete x1
X_opt = X[:,[0,3,5]]
regressor_OLS = sm.OLS(endog = y, exog=X_opt).fit()
regressor_OLS.summary()
```