---
title: OpenStack 虚拟机状态卡死之处理方法
date: 2017-09-15 14:57:33
tags: [OpenStack, nova]
categories: OpenStack实践笔记
---

</br>
<font size=4>方法:</font>
<font size=3>
1. nova list  //找到对应的vm id
2. nova reset-state id //重设vm状态
3. nova stop id //关闭vm电源
4. nova start id
</font>
</br>

<font size=3 color=red>注意: </font>
<font sizze=3>
&emsp;&emsp; nova 操作需要user, tenant信息，只有设定好这个才能查看到对应user和tenant下的vm信息，而且user需要是该tenant的管理员。
</font>

<font size=3 color=red>
A legal openrc maybe looks like this:</br>

```
export OS_USERNAME=xxx
export OS_PASSWORD=****
export OS_TENANT_NAME=xxx
export OS_AUTH_URL=http://controller:35357/v2.0

```
</font>
