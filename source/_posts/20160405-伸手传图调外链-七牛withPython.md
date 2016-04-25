---
title: 伸手传图调外链 七牛withPython
categories:
  - Python
tags:
  - Python
  - 自娱自乐
  - 七牛
  - Win32Api
  - PIL
date: 2016-04-05 15:44:47
---

懒人创造世界嘛
上传图片到图床再找外链太累人了，还是写一个脚本吧

之前看mac上是有大神写了脚本的，直接检查剪贴板是否有图片，如果有就调用七牛的SDK上传到对应的个人存储空间里，并返回外链地址存在剪贴板，听上去非常地美好。

七牛的API之前看到是有支持Python的SDK的，那还不是信手拈来吗，嘿嘿嘿



初步构思：
===
1. 调用Win32Api检查剪贴板是否有图片
2. 调用SDK上传
3. 记录返回外链
4. 调用Win32Api保存外链地址到剪贴板，最好就是Markdown格式的

可能遇到的困难
===
1. Win32Api并没有接触过剪贴板什么的，不行也可以用上传文件的方式来work around
2. 得装一个winHotKey还是什么的来触发脚本

有问题没啥，一个一个来解决嘛，先来试试再说。

<!-- more -->

七牛Python SDK安装
===

直接安装:

```bash
pip install qiniu
或
easy_install qiniu
```
源码安装：

```bash
# 从Python SDK 下载地址下载源码
tar xvzf python-sdk-$VERSION.tar.gz
cd python-sdk-$VERSION
python setup.py install
```

初始化

在使用SDK 前，您需要一对有效的 AccessKey 和 SecretKey 签名授权。

可以通过如下步骤获得：

    开通七牛开发者帐号
    登录七牛开发者平台，查看 Access Key 和 Secret Key。

获取Access Key 和 Secret Key 后，调用如下两行代码进行初始化对接：

```python
from qiniu import Auth

q = Auth(access_key, secret_key)
```


## 文件上传

上传流程

为了尽可能地改善终端用户的上传体验，七牛云存储首创了客户端直传功能。更多信息请参阅业务流程。

上传代码：
```python
# -*- coding: utf-8 -*-
# flake8: noqa

from qiniu import Auth, put_file, etag, urlsafe_base64_encode
import qiniu.config

#需要填写你的 Access Key 和 Secret Key
access_key = 'Access_Key'
secret_key = 'Secret_Key'

#构建鉴权对象
q = Auth(access_key, secret_key)

#要上传的空间
bucket_name = 'Bucket_Name'

#上传到七牛后保存的文件名
key = 'my-python-logo.png';

#生成上传 Token，可以指定过期时间等
token = q.upload_token(bucket_name, key, 3600)

#要上传文件的本地路径
localfile = './sync/bbb.jpg'

ret, info = put_file(token, key, localfile)
print(info)
assert ret['key'] == key
assert ret['hash'] == etag(localfile)
```

后面还有几个高级接口，这回应该是用不到的。
不用的就不看了，省的头晕...

---

本来还以为会有一个接口来使用文件token来查找外链地址

获取剪贴板图片数据
===

然后找到了一个大神做的脚本，获取win32api剪贴板内容并保存图片到本地，来自[ 使用Python保存屏幕截图 ](http://www.cnblogs.com/xieqiankun/p/usePythonForScreenShot.html)



```python
import ctypes
from ctypes.wintypes import *
import win32clipboard
from win32con import *
import sys

class BITMAPFILEHEADER(ctypes.Structure):
    _pack_ = 1  # structure field byte alignment
    _fields_ = [
        ('bfType', WORD),  # file type ("BM")
        ('bfSize', DWORD),  # file size in bytes
        ('bfReserved1', WORD),  # must be zero
        ('bfReserved2', WORD),  # must be zero
        ('bfOffBits', DWORD),  # byte offset to the pixel array
    ]
SIZEOF_BITMAPFILEHEADER = ctypes.sizeof(BITMAPFILEHEADER)

class BITMAPINFOHEADER(ctypes.Structure):
    _pack_ = 1  # structure field byte alignment
    _fields_ = [
        ('biSize', DWORD),
        ('biWidth', LONG),
        ('biHeight', LONG),
        ('biPLanes', WORD),
        ('biBitCount', WORD),
        ('biCompression', DWORD),
        ('biSizeImage', DWORD),
        ('biXPelsPerMeter', LONG),
        ('biYPelsPerMeter', LONG),
        ('biClrUsed', DWORD),
        ('biClrImportant', DWORD)
    ]
SIZEOF_BITMAPINFOHEADER = ctypes.sizeof(BITMAPINFOHEADER)

win32clipboard.OpenClipboard()
try:
    if win32clipboard.IsClipboardFormatAvailable(win32clipboard.CF_DIB):
        data = win32clipboard.GetClipboardData(win32clipboard.CF_DIB)
    else:
        print('clipboard does not contain an image in DIB format')
        sys.exit(1)
finally:
    win32clipboard.CloseClipboard()

bmih = BITMAPINFOHEADER()
ctypes.memmove(ctypes.pointer(bmih), data, SIZEOF_BITMAPINFOHEADER)

if bmih.biCompression != BI_BITFIELDS:  # RGBA?
    print('insupported compression type {}'.format(bmih.biCompression))
    sys.exit(1)

bmfh = BITMAPFILEHEADER()
ctypes.memset(ctypes.pointer(bmfh), 0, SIZEOF_BITMAPFILEHEADER)  # zero structure
bmfh.bfType = ord('B') | (ord('M') << 8)
bmfh.bfSize = SIZEOF_BITMAPFILEHEADER + len(data)  # file size
SIZEOF_COLORTABLE = 0
bmfh.bfOffBits = SIZEOF_BITMAPFILEHEADER + SIZEOF_BITMAPINFOHEADER + SIZEOF_COLORTABLE

bmp_filename = 'clipboard.bmp'
with open(bmp_filename, 'wb') as bmp_file:
    bmp_file.write(bmfh)
    bmp_file.write(data)

print('file "{}" created from clipboard image'.format(bmp_filename))

```
本地跑了一下，确实好用的很

那就用这俩东东调整一下好了，第一件事儿是给文件命名，两个范例都是用的写死的名称，为了能快速上传文件，随机数我打算使用到ms的随机数

```python
import datetime

timeS= datetime.datetime.now().strftime('%Y%m%d_%H%M%S_%f')
fileName = 'clipboard_tmp_'+str(timeS)+'.bmp'
print fileName

#output
#clipboard_tmp_20160401_192159_471000.bmp
```
复杂的不见得会，这个还是很熟练的...

那就果断先把他们拼起来，
基本上就是这个样儿

```python
# -*- coding: utf-8 -*-
import datetime
import ctypes
from ctypes.wintypes import *
import win32clipboard
from win32con import *
import sys
from qiniu import Auth, put_file, etag, urlsafe_base64_encode
import qiniu.config

#需要填写你的 Access Key 和 Secret Key
access_key = '七牛提供的AccessKey'
secret_key = '七牛给的秘钥'
temp_url_Base = '七牛给的外链基地址'

#构建鉴权对象
q = Auth(access_key, secret_key)

#要上传的空间
bucket_name = 'evinoca'


def getFileName():
    timeS= datetime.datetime.now().strftime('%Y%m%d_%H%M%S_%f')
    fileName = 'clipboard_tmp_'+str(timeS)+'.bmp'
    return fileName

class BITMAPFILEHEADER(ctypes.Structure):
    _pack_ = 1  # structure field byte alignment
    _fields_ = [
        ('bfType', WORD),  # file type ("BM")
        ('bfSize', DWORD),  # file size in bytes
        ('bfReserved1', WORD),  # must be zero
        ('bfReserved2', WORD),  # must be zero
        ('bfOffBits', DWORD),  # byte offset to the pixel array
    ]
SIZEOF_BITMAPFILEHEADER = ctypes.sizeof(BITMAPFILEHEADER)

class BITMAPINFOHEADER(ctypes.Structure):
    _pack_ = 1  # structure field byte alignment
    _fields_ = [
        ('biSize', DWORD),
        ('biWidth', LONG),
        ('biHeight', LONG),
        ('biPLanes', WORD),
        ('biBitCount', WORD),
        ('biCompression', DWORD),
        ('biSizeImage', DWORD),
        ('biXPelsPerMeter', LONG),
        ('biYPelsPerMeter', LONG),
        ('biClrUsed', DWORD),
        ('biClrImportant', DWORD)
    ]
SIZEOF_BITMAPINFOHEADER = ctypes.sizeof(BITMAPINFOHEADER)

def getClipboardAndSave():
    win32clipboard.OpenClipboard()
    try:
        if win32clipboard.IsClipboardFormatAvailable(win32clipboard.CF_DIB):
            data = win32clipboard.GetClipboardData(win32clipboard.CF_DIB)
        else:
            print('clipboard does not contain an image in DIB format')
            sys.exit(1)
    finally:
        win32clipboard.CloseClipboard()

    bmih = BITMAPINFOHEADER()
    ctypes.memmove(ctypes.pointer(bmih), data, SIZEOF_BITMAPINFOHEADER)

    if bmih.biCompression != BI_BITFIELDS:  # RGBA?
        print('insupported compression type {}'.format(bmih.biCompression))
        sys.exit(1)

    bmfh = BITMAPFILEHEADER()
    ctypes.memset(ctypes.pointer(bmfh), 0, SIZEOF_BITMAPFILEHEADER)  # zero structure
    bmfh.bfType = ord('B') | (ord('M') << 8)
    bmfh.bfSize = SIZEOF_BITMAPFILEHEADER + len(data)  # file size
    SIZEOF_COLORTABLE = 0
    bmfh.bfOffBits = SIZEOF_BITMAPFILEHEADER + SIZEOF_BITMAPINFOHEADER + SIZEOF_COLORTABLE

    bmp_filename = getFileName()
    with open(bmp_filename, 'wb') as bmp_file:
        bmp_file.write(bmfh)
        bmp_file.write(data)
    print('file "{}" created from clipboard image'.format(bmp_filename))
    return bmp_filename

def upload(fileName):
    #上传到七牛后保存的文件名
    key = fileName;
    #生成上传 Token，可以指定过期时间等
    token = q.upload_token(bucket_name, key, 3600)
    #要上传文件的本地路径
    localfile = 'C:\Users\Administrator\Desktop'+'\\'+fileName
    ret, info = put_file(token, key, localfile)
    print(info)
    return

if __name__ == '__main__':
    upload(getClipboardAndSave())
```

把几个现成的东西一拼就完了，不过跑了一下发现Bmp文件格式实在是太大了，在现在的显示器动不动就是2k 27寸的情况下，我俩显示器一个完整截图将近10MB，上传占用的时间让人感动
所以必须要再转换一下文件格式，再生成一个markdown的外链字符串就完事儿了，嘿嘿

---
格式转换
===
本来觉得这个不应该是小菜吗，直接用PIL库就行了
特别简单

```python
import Image
def convertBmp2Png(bmpFilename,pngFilename):
    Image.open(bmpFilename).save(pngFilename)
    return pngFilename
```
就是把图片打开，另存为，简单粗暴

一直抛异常

```python
Traceback (most recent call last):
  File "C:\Users\Administrator\Desktop\getRandomTimeStamp.py", line 108, in <module>
    upload(getClipboardAndSave())
  File "C:\Users\Administrator\Desktop\getRandomTimeStamp.py", line 93, in getClipboardAndSave
    convertBmp2Png(bmp_filename,png_filename)
  File "C:\Users\Administrator\Desktop\getRandomTimeStamp.py", line 30, in convertBmp2Png
    Image.open(bmpFilename).save(pngFilename)
  File "C:\Python27\lib\site-packages\PIL\Image.py", line 1439, in save
    save_handler(self, fp, filename)
  File "C:\Python27\lib\site-packages\PIL\JpegImagePlugin.py", line 471, in _save
    ImageFile._save(im, fp, [("jpeg", (0,0)+im.size, 0, rawmode)])
  File "C:\Python27\lib\site-packages\PIL\ImageFile.py", line 494, in _save
    for e, b, o, a in tile:
ValueError: Not a valid numbers of quantization tables. Should be between 2 and 4.
```


折腾了半天也没搞明白，直到看到这个兄弟的代码
[wxPython写的图片格式转换工具ImageConverter](http://www.programlife.net/wxpython-image-converter.html)
怎么看怎么都跟我找的资料写的一样，试着跑了一下发现except都抛的一模一样的

但是！

桌面上出现了jpg文件啊，什么鬼


---
仔细一看我自己写的那个也有文件输出...
那么以结果优先的话，就让它抛着玩儿吧，反正不花钱
```python
import Image
def convertBmp2Png(bmpFilename,pngFilename):
    try:
        Image.open(bmpFilename).save(pngFilename)
    except:
        pass
    return pngFilename
```
就这么着了！


写剪贴板
===
之前已经用过剪贴板的库win32clipboard，这个按理说也很简单对吧
网上的说法一致都是与[Python读写剪贴板 ](http://blog.log4d.com/2010/10/python-clipboard/)一样一样的
```python
import win32clipboard
from win32con import *

w.OpenClipboard()
d = w.GetClipboardData(win32con.CF_TEXT)
w.CloseClipboard()
print d

w.OpenClipboard()
w.EmptyClipboard()
w.SetClipboardData(win32con.CF_TEXT, aString)
w.CloseClipboard()
```
偏偏在我这儿不好用，系统剪贴板都乱套了

最后选择用了更偷懒的办法，

>clipboard 0.0.4

>A cross platform clipboard operation library of Python. Works for Windows, Mac and Linux.
Well, as I was trying to implement this, I realize that everything is included in pyperclip already: https://pypi.python.org/pypi/pyperclip/
But anyway, clipboard is a better name. You are free to choose:-) I might add more features to it.
Usage:
```
import clipboard
clipboard.copy("abc")  # now the clipboard content will be string "abc"
text = clipboard.paste()  # text will have the content of clipboard
```
平易近人得都要哭了

组装到一起
===

```python
# -*- coding: utf-8 -*-
import datetime
import ctypes
from ctypes.wintypes import *
import win32clipboard
from win32con import *
import sys
from qiniu import Auth, put_file, etag, urlsafe_base64_encode
import qiniu.config
import Image
import win32clipboard as w 
import win32con
import clipboard

#需要填写你的 Access Key 和 Secret Key
access_key = '七牛给的access_key'
secret_key = '七牛给的secret_key'
temp_url_Base = '七牛给的temp_url_Base'

#构建鉴权对象
q = Auth(access_key, secret_key)

#要上传的空间
bucket_name = 'evinoca'


def getFileName():
    timeS= datetime.datetime.now().strftime('%Y%m%d_%H%M%S_%f')
    fileName = 'clipboard_tmp_'+str(timeS)
    return fileName

def convertBmp2Png(bmpFilename,pngFilename):
    try:
        Image.open(bmpFilename).save(pngFilename)
    except:
        pass
    return pngFilename

class BITMAPFILEHEADER(ctypes.Structure):
    _pack_ = 1  # structure field byte alignment
    _fields_ = [
        ('bfType', WORD),  # file type ("BM")
        ('bfSize', DWORD),  # file size in bytes
        ('bfReserved1', WORD),  # must be zero
        ('bfReserved2', WORD),  # must be zero
        ('bfOffBits', DWORD),  # byte offset to the pixel array
    ]
SIZEOF_BITMAPFILEHEADER = ctypes.sizeof(BITMAPFILEHEADER)

class BITMAPINFOHEADER(ctypes.Structure):
    _pack_ = 1  # structure field byte alignment
    _fields_ = [
        ('biSize', DWORD),
        ('biWidth', LONG),
        ('biHeight', LONG),
        ('biPLanes', WORD),
        ('biBitCount', WORD),
        ('biCompression', DWORD),
        ('biSizeImage', DWORD),
        ('biXPelsPerMeter', LONG),
        ('biYPelsPerMeter', LONG),
        ('biClrUsed', DWORD),
        ('biClrImportant', DWORD)
    ]
SIZEOF_BITMAPINFOHEADER = ctypes.sizeof(BITMAPINFOHEADER)

def getClipboardAndSave():
    win32clipboard.OpenClipboard()
    try:
        if win32clipboard.IsClipboardFormatAvailable(win32clipboard.CF_DIB):
            data = win32clipboard.GetClipboardData(win32clipboard.CF_DIB)
        else:
            print('clipboard does not contain an image in DIB format')
            sys.exit(1)
    finally:
        win32clipboard.CloseClipboard()

    bmih = BITMAPINFOHEADER()
    ctypes.memmove(ctypes.pointer(bmih), data, SIZEOF_BITMAPINFOHEADER)

    if bmih.biCompression != BI_BITFIELDS:  # RGBA?
        print('insupported compression type {}'.format(bmih.biCompression))
        sys.exit(1)

    bmfh = BITMAPFILEHEADER()
    ctypes.memset(ctypes.pointer(bmfh), 0, SIZEOF_BITMAPFILEHEADER)  # zero structure
    bmfh.bfType = ord('B') | (ord('M') << 8)
    bmfh.bfSize = SIZEOF_BITMAPFILEHEADER + len(data)  # file size
    SIZEOF_COLORTABLE = 0
    bmfh.bfOffBits = SIZEOF_BITMAPFILEHEADER + SIZEOF_BITMAPINFOHEADER + SIZEOF_COLORTABLE

    baseName= getFileName()
    bmp_filename = baseName+".bmp"
    png_filename = baseName+".jpg"
    with open(bmp_filename, 'wb') as bmp_file:
        bmp_file.write(bmfh)
        bmp_file.write(data)
    print('file "{}" created from clipboard image'.format(bmp_filename))
    convertBmp2Png(bmp_filename,png_filename)
    return png_filename

def upload(fileName):
    #上传到七牛后保存的文件名
    key = fileName;
    #生成上传 Token，可以指定过期时间等
    token = q.upload_token(bucket_name, key, 3600)
    #要上传文件的本地路径，我的脚本目前就放在桌面上，所以索引路径仍然是写死的
    localfile = 'C:\Users\Administrator\Desktop'+'\\'+fileName
    ret, info = put_file(token, key, localfile)
    print(info)        
    return temp_url_Base+'//'+fileName

if __name__ == '__main__':
    clipboard.copy(upload(getClipboardAndSave()))

```

总结
===
在windows环境下，只要剪贴板里面有图片数据，运行脚本就可以一键式上传到图床上，并把地址扔到剪贴板里。
虽然简陋了点，但是能用就行，后面再优化呗

1. 写Python脚本真的跟搬砖没两样
2. 要善用Pypi的库
3. 写博客第二天看就觉的对不起语文老师

我写的第一篇技术随笔就这么愉快的结束了