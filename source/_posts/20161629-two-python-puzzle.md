---
title: 滥竽充数：随手记两个Python小题目
date: 2016-06-29 14:27:44
categories: Python
tags: Python
---

原来还想着向大神们学习一周好歹写一个博客来着，被各种杂事儿耽误了才发现难怪我就是一个俗人...

最近碰到了两个小问题，把答案记下来勉强当做自己有一些提高吧...



# 环绕读取矩阵

有一个二维数组，类似矩阵。

```python
testMatrix = [[ 1, 2, 3, 4, 5, 6, 7, 8, 9],
              [10,11,12,13,14,15,16,17,18],
              [19,20,21,22,23,24,25,26,27],
              [28,29,30,31,32,33,34,35,36],
              [37,38,39,40,41,42,43,44,45],
              [46,47,48,49,50,51,52,53,54]]
```

需要按照1~9,18,27,36,45,54~46这种顺序顺时针读取数据，到10的时候向内部跳转

结题思路就是判断每次到达顶角位置转向，完成一圈之后向内部偏移一个位置

虽然现在想着挺清楚的，当时听到这么一个题儿那叫一个蒙

```python
# -*- coding: utf-8 -*-
'''
Created on 2016年6月23日

@author: Audric Sun
'''

testMatrix = [[ 1, 2,   3,  4,  5,  6,  7, 8,  9],
                    [10,11,12,13,14,15,16,17,18],
                    [19,20,21,22,23,24,25,26,27],
                    [28,29,30,31,32,33,34,35,36],
                    [37,38,39,40,41,42,43,44,45],
                    [46,47,48,49,50,51,52,53,54]]


def readMatrix(matrix):
    l1 = len(matrix)
    l2 = len(matrix[0])
    print l1,l2
    layer = 0
    pos = [0,0]
    offset = 0
    d = 0
    c = l1*l2
    while c>0:
        
        print pos,d,offset,c,matrix[pos[0]][pos[1]]
        
        if pos[0]==0+offset and pos[1]==0+offset:
            d = 0            
        elif  pos[0]==0+offset and pos[1]==l2-offset-1 :
            d = 1
        elif  pos[0]==l1-offset-1 and pos[1]==l2-offset-1 :
            d = 2
        elif  pos[0]==l1-offset-1 and pos[1]==0+offset :    
            d = 3
        else:
            d = d
        
        if d == 0 :
            pos[1]+=1
        elif d ==1:
            pos[0]+=1
        elif d==2:
            pos[1]-=1
        elif d==3:
            pos[0]-=1
        else:
            raise
        
        if pos==[0+offset,0+offset]:
            offset+=1
            pos[0]+=1
            pos[1]+=1
        
        c-=1
if __name__ == '__main__':
    readMatrix(testMatrix)
```

# 计算多维数组

给定三组多维数组

[1]=1*1

[1,[2]]=1+2\*2

[1,[2,[3]]]=1+2\*2+3\*3

设计一个算法来计算类似多维数组结果

思路：

递归呗...

```python
# -*- coding: utf-8 -*-
'''
Created on 2016年6月23日

@author: Audric Sun
'''

list = [1,[2,[3,[4,[5,[6,[7]]]]]]]

def calc(list,recrusiveDepth=1):
    print list,recrusiveDepth
    r = 0
    for item in list:
        if type(item)==type(1):
            r=r+(item*recrusiveDepth)
        elif type(item)==type([]):
            r=r+calc(item,recrusiveDepth+1)
        else:
            raise AttributeError,"Args must be int"
    print "Depth level:"+str(recrusiveDepth)+" Ret:"+str(r)
    return r


print calc(list)
```

```shell
>>> 
[1, [2, [3, [4, [5, [6, [7]]]]]]] 1
[2, [3, [4, [5, [6, [7]]]]]] 2
[3, [4, [5, [6, [7]]]]] 3
[4, [5, [6, [7]]]] 4
[5, [6, [7]]] 5
[6, [7]] 6
[7] 7
Depth level:7 Ret:49
Depth level:6 Ret:85
Depth level:5 Ret:110
Depth level:4 Ret:126
Depth level:3 Ret:135
Depth level:2 Ret:139
Depth level:1 Ret:140
140
```

