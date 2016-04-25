---
title: 考勤时间统计 with Python （1）
categories:
  - Python
tags:
  - Python
date: 2016-04-01 18:02:03
---

半年前行政同事说需要搞一个考勤管理工具，搞就搞吧，搞成一坨我也没办法。

希望不要太给测试部门丢人...

大概思路
===
基本上能给到的数据是两个excel表，一个是打卡机出来的数据，一个是员工花名册。
输出的东西希望是非常直观的数据，并且可以编辑，可以分发。减少行政同事的工作量。

输入
---
1. 考勤记录-excel文件
2. 组织结构-excel文件
3. 对于由于法定假期和调休导致的考勤异常需要进行条件判断


输出
---
1. 仍然是excel文件

计算
---
1. 统计每一个人的每一天的考勤时间
2. 按照行政规定统计每人每月考勤异常次数

这么一看需求很简单嘛
简单画一个图来看看就是：

![class diagram](http://7xsfv0.com1.z0.glb.clouddn.com/Class%20Diagram.png)

<!-- more -->

读取excel，存储
===
第一句写的就是这个
```python
from xlrd import open_workbook
import xlrd
```