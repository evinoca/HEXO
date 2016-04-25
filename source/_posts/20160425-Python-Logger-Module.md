---
title: 20160425 Python Logger Module
date: 2016-04-25 18:27:21
categories: Python
tags: [Python]
---



# Python中的Logging模快

平常工作中最喜欢的就是热爱打log的开发同事
因为我们实际上是做的黑盒顶多算是个灰盒的活儿，没有log就啥也看不出来。
顶多算是知道这个东西跟说好的不太一样，到底是怎么不一样完全搞不明白。有log就不一样了，虽然代码看不明白，看打印还是知道在那个方法出了问题的。

到了自己这儿吧，虽然我一直秉承着优秀的测试人员的优良品德，写代码都是做的测试驱动开发：任何一个模块都恨不得先把单元测试先给准备好才开始动手。
开始的时候还好办，单元测试全部用print就行。
Python里面基本上啥都能打印，所有变量都很友好，但是到了后来就发现...这完全不是那么个事儿

调试的时候到处都有print，我还有时候心情很好会打一个TODO，后来事儿多了就顾不上了，最后还得在一堆熟悉的陌生码中找我的print再一个一个删掉

果然还是得靠logging模块啊，这货开始虽然有点儿闹心，但是用好了真的是debug挚友

模块其实还是挺友好的，就是我自己记不住代码，每次都得去过去做过的文件里去找。

这回记好了再去代码里找我就...是真傻

# 基础使用
调用模块自后的第一件事儿就是给起一个名字就是getLogger


```python
import logging    

logger = logging.getLogger("spam_application")  
logger.setLevel(logging.DEBUG) 

ch = logging.StreamHandler()  
ch.setLevel(logging.DEBUG) 

formatter = logging.Formatter("%(asctime)s - %(name)s - %(levelname)s - %(message)s")  

ch.setFormatter(formatter)  
logger.addHandler(ch)  

logger.info("creating an instance of auxiliary_module.Auxiliary") 
```

    2016-04-25 16:42:34,802 - spam_application - INFO - creating an instance of auxiliary_module.Auxiliary
    
<!--more-->

setLevel： 配置logger的告警等级

StreamHandler() 是使用STDOUT打印到控制台的句柄
也可以使用 logging.FileHandler("spam.log")  来打印到log文件上
在面向对象设计的时候，会有多个对象同时打印log，这个时候需要给每一个logger分别配置

```python
# spam_application.py

import logging
# create logger
module_logger = logging.getLogger("spam_application.auxiliary")



class Auxiliary:
    def __init__(self):
        self.logger = logging.getLogger("spam_application.auxiliary.Auxiliary")
        self.logger.info("creating an instance of Auxiliary")
    def do_something(self):
        self.logger.info("doing something")
        a = 1 + 1
        self.logger.info("done doing something")
        
        
def some_function():
    module_logger.info("received a call to some_function")

```


```python
# spam_application.py
import logging    
import auxiliary_module
    
# create logger with "spam_application"  
logger = logging.getLogger("spam_application")  
logger.setLevel(logging.DEBUG)  
# create file handler which logs even debug messages  
fh = logging.FileHandler("spam.log")  
fh.setLevel(logging.DEBUG)  
# create console handler with a higher log level  
ch = logging.StreamHandler()  
ch.setLevel(logging.DEBUG)  
# create formatter and add it to the handlers  
formatter = logging.Formatter("%(asctime)s - %(name)s - %(levelname)s - %(message)s")  
fh.setFormatter(formatter)  
ch.setFormatter(formatter)  
# add the handlers to the logger  
logger.addHandler(fh)  
logger.addHandler(ch)  



logger.info("creating an instance of auxiliary_module.Auxiliary")  
a = auxiliary_module.Auxiliary()  
logger.info("created an instance of auxiliary_module.Auxiliary")  
logger.info("calling auxiliary_module.Auxiliary.do_something")  
a.do_something()  
logger.info("finished auxiliary_module.Auxiliary.do_something")  
logger.info("calling auxiliary_module.some_function()")  
auxiliary_module.some_function()  
logger.info("done with auxiliary_module.some_function()")
```

对，就这样。

# 总结

logger = logging.getLogger() # 配置logger的名称
logger.setLevel(logging.DEBUG) # 配置告警级别
fh = logging.FileHandler("spam.log") 配置输出句柄
formatter = logging.Formatter("%(asctime)s - %(name)s - %(levelname)s - %(message)s")  # 配置输出格式
fh.setFormatter(formatter)  # 配置格式到句柄
logger.addHandler(fh) # 添假句柄配置
logger.info("something to be printed") # 打印log

