---
title: HMM学习笔记
date: 2017-11-13 21:49:35
tags: [NLP, 算法]
categories: 模型学习笔记
mathjax: true
---

<h4>1. 简介</h4>
&emsp;隐马尔克夫模型可以用两组状态集合和三组概率集合来描述：</br>
&emsp;&emsp;(1) 隐藏状态: 隐藏在观察状态后的状态</br>
&emsp;&emsp;(2) 观察状态: 表象看到的状态</br>
&emsp;&emsp;(3) 初始概率集合: 包含了隐状态在t=1时刻的初始选择概率</br>
&emsp;&emsp;(4) 状态转移矩阵: 包含了隐藏状态到另一个隐藏状态的概率</br>
&emsp;&emsp;(5) 混淆矩阵: 包含了隐马尔克夫模型的某一个隐藏状态观察到某个观察状态的概率。</br>

&emsp;&emsp;● HMM有一个最重要也是非常不符合实际的假设：以上各种概率不随时间的迁移而改变.</br>
&emsp;&emsp;● 因此一个HMM是在一个马尔克夫过程中引入一组隐藏状态以及与其相关的观察状态的概率关系。

<h4>2. 定义</h4>
&emsp;一个HMM是一个三元组(p1, A, B)。</br>
&emsp;![](1.png)
&emsp;&emsp;其中X<sub>i<sub>j</sub></sub>表示j时刻的隐藏状态为X<sub>i</sub>, i \in {1...n}, n为隐藏状态的书目。
text2

<h4>3. 应用</h4>
&emsp;


