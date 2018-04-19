title: numpy pandas快速入门
author: gsal
tags:
  - 机器学习
categories:
  - 机器学习
date: 2018-03-27 10:43:00
---
[https://github.com/QCaudron/pydata_pandas/blob/master/coffee_analysis_exercise.ipynb](https://github.com/QCaudron/pydata_pandas/blob/master/coffee_analysis_exercise.ipynb)
[https://www.youtube.com/watch?v=5XGycFIe8qE](https://www.youtube.com/watch?v=5XGycFIe8qE)  
 [https://www.youtube.com/watch?v=POe1cufDWFs](https://www.youtube.com/watch?v=POe1cufDWFs)
### numpy
#### numpy数组属性

``` python
import numpy as np
array = np.array([[1,2,3],
				2,3,4]])
array.ndim
array.shape
array.size
```

#### numpy创建array

``` python
a = np.array([2,23,4], dtype = np.int) #np.int,float32, int32 
np.zeros((3,4))
np.ones((3,4),dtype = np.int16)
np.arrange(10,20,2)
np.arrange(12).reshage((3,4))
```