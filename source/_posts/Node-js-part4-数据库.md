title: Node.js part4 数据库
author: gsal
tags:
  - node
categories: []
date: 2018-05-02 13:01:00
---
Nodejs常使用mongodb，mongodb为nosql，里面存的也是json，所以就是js从前端到后端再到数据库一条龙全栈了。
NoSQL是什么？Not Only SQL，有些nosql数据库也可以用sql语句。    
nosql数据库是非关系型的数据库，在大数据和实时的web应用中使用。具有弹性。
#### NoSQL的优势
1. 能够处理大数据
1. 具有弹性，没有事先定义的schema，没有事先定义的结构，不用定义行、列之类的
1. nosql能够处理非结构化的数据
1. Scaling-量级变化，随着数据量的增长的表现，scale-out 

![scale](/images/image222.png)
scale-out:只用向原有的系统添加一个新机器
scale-up:为了scale，需要添加组件，添加存储空间，内存，cpu，能源等等，更大更强的机器



#### 关系型数据库的优点
1. 更适用关系型数据
1. Normalization 正规化
1. 语言支持（sql）
1. 数据一致性（有外键等等约束）
1. ACID的服从

#### NoSQL数据库的分类
1. 文档数据库（MongoDB，CouchDB）
1. 列数据库（Apache Cassandra）
1. Key-Value （Redis，Couchbase Server） 大量数据，但是数据简单
1. Cache-Systems
1. Graph Databases（Neo4J） 所有的数据都作为一个节点，跟其他数据相连


![对比](/images/image111.png)
