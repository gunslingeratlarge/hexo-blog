title: 'Problem:Not enough memory to mine ether'
author: gsal
tags:
  - 区块链
  - 以太坊
categories:
  - 区块链
date: 2018-04-08 20:03:00
---
https://github.com/ethereum/go-ethereum/issues/14786  
> ERROR[07-10|15:56:35] Failed to generate mapped ethash dataset epoch=1 err="cannot allocate memory"
runtime: out of memory: cannot allocate 2164260864-byte block (128057344 in use)
fatal error: out of memory

实际上是使用了错误的geth版本导致的（64位使用了32位） geth version,如果architecture是386的话说明是32位的。跟内存没什么关系。