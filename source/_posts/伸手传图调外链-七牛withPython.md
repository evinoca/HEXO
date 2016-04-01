---
title: 伸手传图调外链 七牛withPython
categories:
  - Python
tags:
  - Python
  - 自娱自乐
  - 七牛
date: 2016-04-01 18:16:35
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

## 上传&回调
```python
# -*- coding: utf-8 -*-
# flake8: noqa

from qiniu import Auth, put_file, etag,
import qiniu.config

access_key = 'Access_Key'
secret_key = 'Secret_Key'

q = Auth(access_key, secret_key)

bucket_name = 'Bucket_Name'

key = 'my-python-logo.png';

#上传文件到七牛后， 七牛将文件名和文件大小回调给业务服务器。
policy={
 'callbackUrl':'http://your.domain.com/callback.php',
 'callbackBody':'filename=$(fname)&filesize=$(fsize)'
 }

token = q.upload_token(bucket_name, key, 3600, policy)

localfile = './sync/bbb.jpg'

ret, info = put_file(token, key, localfile)
print(info)
assert ret['key'] == key
assert ret['hash'] == etag(localfile)
```
后面还有几个高级接口，这回应该是用不到的。
不用的就不看了，省的头晕...

先把SDK给的范例改一下