---
title: /proc/cpuinfo文本分析
date: 2017-09-18 15:42:02
tags: [Linux, cpuinfo]
categories: Linux操作系统小知识
---

&emsp;&emsp;在linux系统中，如果想了解系统中CPU的相关信息，可以通过/proc/cpuinfo文件找到。本文章针对该文件某些输出项进行介绍。
</br></br>
&emsp;&emsp;processor　：系统中逻辑处理核的编号</br> 
&emsp;&emsp;vendor_id　：CPU制造商</br>
&emsp;&emsp;cpu family　：CPU产品系列代号</br>
&emsp;&emsp;model　　　：CPU属于其系列中的哪一代的代号</br>
&emsp;&emsp;model name：CPU属于的名字及其编号、标称主频</br>
&emsp;&emsp;cpu MHz　  ：CPU的实际使用主频</br>
&emsp;&emsp;cache size   ：CPU二级缓存大小</br>
&emsp;&emsp;physical id   ：单个CPU的标号</br>
&emsp;&emsp;siblings       ：单个CPU逻辑物理核数</br>
&emsp;&emsp;core id        ：当前逻辑核所在的物理核在其所处CPU中的编号</br>
&emsp;&emsp;cpu cores    ：该逻辑核所处CPU的物理核数</br>
&emsp;&emsp;apicid          ：用来区分不同逻辑核的编号，系统中每个逻辑核的此编号必然不同，此编号不一定连续</br>
&emsp;&emsp;fpu             ：是否具有浮点运算单元（Floating Point Unit）</br>
&emsp;&emsp;fpu_exception  ：是否支持浮点计算异常</br>
&emsp;&emsp;cpuid level   ：执行cpuid指令前，eax寄存器中的值，根据不同的值cpuid指令会返回不同的内容<font color=red>???</font></br>
&emsp;&emsp;wp             ：表明当前CPU是否在内核态支持对用户空间的写保护（Write Protection）</br>
&emsp;&emsp;flags          ：当前CPU支持的功能</br>
&emsp;&emsp;bogomips   ：在系统内核启动时粗略测算的CPU速度（Million Instructions Per Second）</br>
&emsp;&emsp;clflush size  ：每次刷新缓存的大小单位</br>
&emsp;&emsp;cache_alignment ：缓存地址对齐单位</br>
&emsp;&emsp;address sizes     ：可访问地址空间位数<font color=red>???</font><br>
</br>
&emsp;&emsp;根据以上内容，我们则可以很方便的知道当前系统关于CPU、CPU的核数、CPU是否启用超线程等信息。</br>
&emsp;&emsp;1. 查询系统具有多少个逻辑核: ```cat /proc/cpuinfo | grep "processor" | wc -l```</br>
&emsp;&emsp;2. 查询系统单个CPU的物理核数：```cat /proc/cpuinfo | grep "cpu cores" | uniq```</br>
&emsp;&emsp;3. 查询系统CPU是否启用超线程：```cat /proc/cpuinfo | grep -e "cpu cores"  -e "siblings" | sort | uniq```<br>
&emsp;&emsp;&emsp;&emsp;<font size=3 color=green>如果cpu cores数量和siblings数量一致，则没有启用超线程，否则超线程被启用。</font></br>
&emsp;&emsp;4. 查询系统CPU的个数：```cat /proc/cpuinfo | grep "physical id" | sort | uniq | wc -l```</font>
