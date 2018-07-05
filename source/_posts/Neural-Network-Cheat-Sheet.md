title: Neural Network Cheat Sheet
author: gsal
tags:
  - 机器学习
  - python
categories:
  - 机器学习
date: 2018-06-26 15:44:00
---
# Neural Network Cheat Sheet
@(Machine Learning By Andrew Ng)[机器学习]
## 介绍
Theano与Tensorflow是两个Deep Learning的库，而Keras在这两个库的基础之上使得我们构建深度神经网络更加地方便和容易。不然就要写很多行代码啦。
### 安装
通过conda安装非常方便，`conda install tensorflow`,`conda install theano`,`conda install keras`,现在凡是深度学习我感觉都会用这三个包，感觉像是三板斧一样。    
通过conda安装可能由于是国外的源比较慢，因此可以换清华大学的源。  


```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes 
```
可以通过~/.condarc查看是否更换源成功。换源之后就能够下得飞快了。  
<!--more-->
## 预处理
在正式构建神经网络之前，我们需要对数据进行预处理，包括将categorical variable改成dummy variable，并且要去掉一行dummy variable以免落入one hot encoder的陷阱。以及feature-scaling和train-test-split等等。  
对于预处理我们也有自己的模板。    
``` python
# Data Preprocessing

# Importing the libraries
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

# Importing the dataset
dataset = pd.read_csv('Data.csv')
X = dataset.iloc[:, :-1].values
y = dataset.iloc[:, 3].values

# Taking care of missing data
from sklearn.preprocessing import Imputer
imputer = Imputer(missing_values = 'NaN', strategy = 'mean', axis = 0)
imputer.fit(X[:, 1:3])
X[:, 1:3] = imputer.transform(X[:, 1:3])

# Encoding categorical data
# Encoding the Independent Variable
from sklearn.preprocessing import LabelEncoder, OneHotEncoder
labelencoder_X = LabelEncoder()
X[:, 0] = labelencoder_X.fit_transform(X[:, 0])
onehotencoder = OneHotEncoder(categorical_features = [0])
X = onehotencoder.fit_transform(X).toarray()
# Encoding the Dependent Variable
labelencoder_y = LabelEncoder()
y = labelencoder_y.fit_transform(y)
```
其实这里预处理就做了两件事情，一件是填充了缺失的数据，一件是处理了categorical data。  

## 选型
### 激励函数
- threshhold function
- sigmoid function：光滑，在output layer比较有用（输出概率）
- Rectifier function：在机器学习非常常用
	-  ![微信截图_20180626090538.png](https://i.loli.net/2018/06/26/5b31911993906.png)

我们在output layer层使用sigmoid函数，在隐层使用rectifier函数。
### 隐层神经元数目
经验上取input层与output层的平均值，如果想要更加精确还需要进行tuning。

## 构造网络
``` python
import keras
from keras.models import Sequential
from keras.layers import Dense

classifier = Sequential()
classifier.add(Dense(output_dim=6,init="uniform",activation="relu",input_dim=11))
classifier.add(Dense(output_dim=6,init="uniform",activation="relu"))
classifier.add(Dense(output_dim=1,init="uniform",activation="sigmoid"))
```
这里的output_dim表示该隐层有6个神经元，用均匀分布来初始化，relu表示rectifier function作为它的激励函数。然后input layer有11个。**注意这里不用添加input层，直接添加第一隐层，因为input层的神经元并不会被激励。**     
只有第一隐层需要添加input_dim参数，接下来的隐层会自动将上一层的output作为下一层的input。  
最后的output层自然它的output dimension就是1了，并且其激励函数换成sigmoid函数以更好地表示概率。如果是多分类问题，那么要改两个地方，output_dim和activation，激励函数应该用softmax，是多分类的sigmoid。   
在组装好了网络之后我们还需要指定网络的loss function和度量指标。
``` python
classifier.compile(optimizer = "adam", loss ="binary_crossentropy", metrics=['accuracy'])
```
sigmoid作为激励，一般loss function选择binary\_crossentropy，如果是softmax作为激励，则选择categorical\_crossentropy。

## 训练与预测网络
``` python
classifier.fit(X_train,y_train,batch_size=15,epochs=100)

y_pred = classifier.predict(X_test)
y_pred = (y_pred > 0.5)
from sklearn.metrics import confusion_matrix
cm = confusion_matrix(y_test,y_pred)

```
batch_size表示我多少行之后进行一次权值的update，epochs表示我整个训练集要过多少遍。   
confusion_matrix的$C_{ij}$表示实际是i结果预测为j的样本数，如$C_{00}$表示实际为negative，预测也为negative，即TN，$C_{01}$表示实际为negtive，但是预测为positive，即FN。以此类推即可。