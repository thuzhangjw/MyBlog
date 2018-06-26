---
title: shell编程-日常范例积累
date: 2017-12-14 17:40:37
tags: [shell]
categories: shell编程范例
---

&emsp;&emsp;根据本人学习经验，对于shell编程来说，如果不是专门的shell编程人员，系统的学习这方面知识很容易忘记，收效甚微。所以我根据日常使用过程中的积累，不断添加使用范例，来达到学习和积累的目的。
所以本文会持续的添加内容。

## awk使用
1. 统计一个像矩阵一样的文件有多少列： 
```
head -n 1 <filename> | awk -F <separator> '{print NF}'
```
