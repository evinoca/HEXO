---
title: 20160413 Say Bye Bye to Python IDLE
date: 2016-04-10 18:59:32
categories: Python
tags: [Python, ptpython, IDE]
---

背景
===

最开始学习python的时候，还是用的sublime Text 2 试用版
对sublime的插件很是挠头的我一直也就是再开一个cmd窗口来调试，出了问题全靠打log和print

然后
===

<!--more-->

后来为了做GUI学wxpython，开始用eclipse+pydev，终于可以debug了也终于可以跟cmd说拜拜了

再后来，由于有些库刚上手需要找个地儿练练手，加上写一些小工具也没啥难度只要拼拼凑凑就行了，我就又开始嫌弃eclipse这种慢吞吞的货，回到了最初的原点：python IDLE

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160415_112402_745000.jpg)

好歹这个东西可以智能提示对吧，也能写点儿小函数试试手，写点儿小代码也是凑活能用的关键还是快对吧

但是费劲的是这玩意儿咋不能写一个行号呢...

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160415_111539_686000.jpg)

每次出了问题，我就趴桌子上一行行数，实在不行再粘到notepad++里面看。

所以这一次看到了这个ipython的大神新出的ptpython工具，我就把膝盖又送出去了。

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160415_111223_200000.jpg)

在windows里面还能搞这么高大上的东西出来

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160415_111307_293000.jpg)

>[Git: ptpython](https://github.com/jonathanslenders/ptpython/)

另外看到了pyVim这种看着就很厉害的东西

![](https://github.com/jonathanslenders/pyvim/raw/master/docs/images/welcome-screen.png)

>[Git: pyvim](https://github.com/jonathanslenders/pyvim)

但是vim这种大神用的东西，我这种二把刀还是不要碰了，踏踏实实地该用啥用啥吧...




总结
===
其实说到最后，还是理论联系实际，大项目用大家伙，小项目用小项目，顺手是最重要的.

所以上班需要看Java还是用IDEA，顺带发现了Jetbrain新的Pycharm貌似很给力
![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160415_123208_533000.jpg)

但是试了试debug还是不太习惯，另外这么久了真的还是习惯了eclipse，所以写python估计会一直保持用Eclipse+pydev吧
![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160415_123300_489000.jpg)

看log用的notepad++，简单粗暴

写blog需要预览markdown折腾了notepad++的插件，半天仍然没有什么简单办法，最后还是回到了看上去Atom上

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160415_111729_600000.jpg)

写到最后吧，我也不知道自己想表达啥了，也可能就是想晒晒工作用的工具吧...
