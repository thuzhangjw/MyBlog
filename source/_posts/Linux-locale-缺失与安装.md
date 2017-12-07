---
title: Linux locale 缺失与安装
date: 2017-12-07 13:32:55
tags: [linux, locale]
categories: Linux操作系统小知识 
---

## 1.一般情况
&emsp;**查看当前locale设置：**
```
    locale
```
&emsp;**查看系统目前可用的locale配置：**
```
    locale -a
```
&emsp; 如果某种locale不在上述列表中出现，可以使用locale-gen命令来安装该种locale。
```
    locale-gen zh_CN.UTF-8
```
&emsp; 上述命令会安装 zh_CN.UTF-8。</br>

&emsp;**修改当前locale配置：** </br>

&emsp;&emsp;可以直接编辑/etc/default/locale文件，也可以使用如下命令来修改该文件:
```
    update-locale LANG=zh_CN.UTF-8
```

## 2.遇到的问题
&emsp;**en_US.UTF-8... cannot open locale definition file 'translit_neutral': No such file or directory failed** </br>
&emsp;**解决：**</br>
&emsp;&emsp;从别的机器上copy /usr/share/i18n 覆盖本机。

## 3. 小结
&emsp;&emsp;**1.** 全新字符库编码信息位于 /usr/share/i18n目录下面，其中SUPPORTED中包含可用的所用字符集。其中的charmaps存的每种字符集的映射信息。使用localedef可以生成字符集，就是locales里面那些东西。但这些并不是系统中能用的字符集。刚才描述的这些/usr/share/i18n里面的文件只能算是可用的字符集，locale -m可以看到列表。</br>
&emsp;&emsp;**2.** 为了让系统能使用，原始的/usr/share/i18n中的文件要经过处理（complile），能用的字符编码在/usr/lib/locale/下面，成为complied字符集。</br>
&emsp;&emsp;**3.** 使用locale-gen可以把原始的/usr/share/i18n中的文件complie成系统能用的/usr/lib/locale/地字符集。所以当使用locale-gen 命令出现由某些文件确实而失败的情况，往往是由/usr/share/i18n下缺少相应文件导致的。</br>
&emsp;&emsp;**4.** /usr/share/i18n里面的东西操作系统无关，不同体系结构不同系统可以通用，若有缺失，从其他地方复制来便可。</br>
&emsp;&emsp;<font size=1>reference: http://blog.csdn.net/yunhuang2010/article/details/8109578</font>







