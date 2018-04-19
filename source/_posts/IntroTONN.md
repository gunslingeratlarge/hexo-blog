title: Intro to NN
author: gsal
tags:
  - 神经网络
  - 机器学习
categories:
  - 机器学习
date: 2018-04-04 07:18:00
---
https://www.youtube.com/watch?v=LSr96IZQknc  
[notebook下载](https://www.youtube.com/redirect?v=LSr96IZQknc&redir_token=1PSWfEiDFjq0P5HiqetvTU0gRbF8MTUyMjg4MTY4MEAxNTIyNzk1Mjgw&event=video_description&q=https%3A%2F%2Fgithub.com%2FJonComo%2Fflowers%2Fblob%2Fmaster%2Fflowers.ipynb)

# 配置环境

- 官网下载conda
- 安装时选择添加path
- conda create -n nnseries
- activate  nnseries
- conda install numpy
- conda install jupyter
- jupyter notebook

<!--more-->

--

# 引入

- 第一个神经网络由三个节点组成，两个引入节点，一个输出节点
- 对应的python代码是：(此处略)
- 我们一开始参数（w1，w2，b）是随机的，因此预测效果不理想，由此引入cost function

--

# cost function

- 平方误差函数
  - 对预测值与实际目标值的差取平方，估算代价的一种方法
  - 假设NN() = b，目标值为4，则cost = (b - 4)^2
  - 为了最小化cost，我们每次减去cost在该点的斜率值

--

# 最简单神经网络

- 加载数据
  - 将数据存在list中
  - 使用data.append添加单条数据
  - 使用data + [[1,2,3],[4,5,6]]添加多条数据
- 绘制数据
  - 画散点图：
    - plt.scatter([data[i][0]], [data[i][1]], c=c,alpha = .2)
    - plt.grid()可以绘制对应每个坐标值的网格
    - alpha可以指定透明度
    - 如果没有特殊指定，都是在一张图上绘制的（在一个cell中的命令）
  - 画sigmoid函数：
    - X = np.linspace(-5, 5, 100) 这里是取100个点，即均分为99段
    - plt.plot(X, sigmoid(X), c="b") # sigmoid in blue
      - 注意这里sigmoid传入的是一个ndarray，这是numpy所独有的一种数据类型
      - 只有ndarray可以对它进行加减乘除相当于是对它的每一位都操作
      - list是不可以的，会报错
- 进行训练
  - 生成随机数
    - 使用np.random.randn()生成在1附近的，标准正态分布随机数
    - 生成w1，w2，b
  - 计算预测值
  - 进行梯度下降
    - 计算cost = （pred - target ）\*\* 2
    - 计算梯度
    - 根据梯度更新w1，w2，b的值
  - 跟踪cost的值
    - 这里要注意，每次都是用随机取点来训练的，因此如果只跟踪每个点的cost的话不能很好的反映整体的情况，因此每隔一段时间都要随机对所有的点计算cost并取平均，跟踪这个值