---
title: 现学现卖 Sensu&Uchiwa 快速上手
date: 2016-05-17 21:53:49
categories: Tools Collector
tags: [Java,运维]
---

# 背景

刚才研究了一下系统监控的事儿，趁着还热乎先记一下..

在上一篇转来的[七个JAVA的系统监控工具](http://evinoca.github.io/2016/05/17/20160517-7-monitor-Tools/)里面，我看了半天，貌似Sensu这个比较符合现在的需求

不管三七二十一，趁手头还有点儿时间，先装上试试再说

Sensu官方很贴心的给出了5分钟快速上手说明，跟着做就是了，走起

![Sensu Quick Start](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160517_185648_614000.jpg)

Ref: [The five minute Sensu Install](https://sensuapp.org/docs/latest/the-five-minute-install)

<!--more-->

## Linux虚拟机

按照要求，第一件事儿就是找一个ubuntu的虚拟机来，正好有同事刚下了14.04的镜像，直接拿来用

虚拟机软件我用的是VMware Workstation 12 Player ，只要不用做商业用途就是免费。

[Download VMware Workstation Player](http://www.vmware.com/products/player/playerpro-evaluation.html)

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160517_190610_615000.jpg)

我现在这算是开发用吧...直接选择新建虚拟机

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160517_190555_073000.jpg)

等待10分钟左右，就有了一个干净的环境，工作天天被环境挣得挠墙，每次看到一个干净的环境都感觉像是走到了森林里一样忍不住想永远留在这里。

## Sensu 安装

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160517_191148_013000.jpg)

可是美好的时间总是短暂的是不，点击左上角那个图标或者Alt+F1+回车召唤出Ubuntu版的spotlight，然后互换Terminal出来

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160517_191921_939000.jpg)

从这里开始，计时五分钟~

1. 安装Sensu源

```shell
wget https://sensuapp.org/install.sh
sudo bash install.sh
sudo apt-get update
```

bash脚本的时候，实际上就是安装两套软件源到本地linux的源文件里

Core software我们一定是要点yes的

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160517_192101_273000.jpg)

后面问

Do you want to install the Sensu Enterprise software repositories? (y/N): 

的时候就点no就可以了，因为如果点了yes会要求填写企业版用户名密码来校验用户,这个自然没有

1. 安装第三方程序

   ```shell
   sudo apt-get -y install redis-server curl jq
   ```


1. 配置Redis默认参数并启动

   ```shell
   sudo update-rc.d redis-server defaults
   sudo /etc/init.d/redis-server start
   ```


1. 安装sensu

   ```shell
   sudo apt-get install sensu
   ```


1. 下载sensu默认配置config.json

   ```shell
   sudo wget -O /etc/sensu/config.json https://sensuapp.org/docs/0.23/files/simple-sensu-config.json
   ```

2. 下载客户端配置

   ```shell
   sudo wget -O /etc/sensu/conf.d/client.json https://sensuapp.org/docs/0.23/files/simple-client-config.json
   ```

3. 启动sensu服务

   ```shell
   sudo /etc/init.d/sensu-server start
   sudo /etc/init.d/sensu-api start
   sudo /etc/init.d/sensu-client start
   ```

4. 测试api服务状态

   ```shell
   curl -s http://127.0.0.1:4567/clients | jq .
   ```

   如果返回值如下就是ok了

   ```shell
   $ curl -s http://127.0.0.1:4567/clients | jq .
   [
     {
       "timestamp": 1458625739,
       "version": "0.23.0",
       "socket": {
         "port": 3030,
         "bind": "127.0.0.1"
       },
       "subscriptions": [
         "dev"
       ],
       "environment": "development",
       "address": "127.0.0.1",
       "name": "client-01"
     }
   ]
   ```

然后一个基本的sensu服务就ok了

## Web UI- Uchiwa

当然还需要一个Web管理页面，Sensu官方只给企业版提供了页面，像是这个样儿：

![img](https://sensuapp.org/docs/0.23/img/five-minute-dashboard-2-7b851c59.png)

但是对于广大的免费玩家，官方还给了一个代替品 Uchiwa（这名字什么鬼...）界面如下：

真是良心官方啊

![](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160517_190915_877000.jpg)

地址在这里：[uchiwa](https://github.com/sensu/uchiwa)

安装起来也很简单：

1. 安装goLang环境

2. 配置Gopath和环境变量

   ```shell
   mkdir $HOME/go && export GOPATH=$HOME/go
   ```

3. 下载godep

   ```
   go get github.com/tools/godep
   ```

4. 下载uchiwa源码

   ```
   go get github.com/sensu/uchiwa && cd $GOPATH/src/github.com/sensu/uchiwa
   ```

5. 安装依赖库

   ​
   普通账号: `npm install --production`
   Linux管理员账号: `npm install --production --unsafe-perm`

6. 将$GoPath/src/github.com/sensu/uchiwa/config.json.example改名为config.json

7. 修改uchiwa配置config.json将uchiwa指向刚才搭好的sensu服务地址

   ```json
   {
     "sensu": [
       {#修改一组地址和端口
         "name": "Test Site",
         "host": "10.10.3.234",
         "port": 4567
       },
       {#这一组暂时可以删掉，也可以保留反正显示不出来
         "name": "Site 2",
         "host": "api2.example.com",
         "port": 4567,
         "ssl": false,
         "insecure": false,
         "user": "",
         "pass": "",
         "path": "",
         "timeout": 5
       }
     ],
     "uchiwa": {#本地配置
       "host": "0.0.0.0",
       "port": 3000,
       "refresh": 5
     }
   }
   ```

   ​

8. 启动dashboard

   ```shell
   godep go run uchiwa.go
   ```

9. 打开浏览器

   访问`http://localhost:3000/`

10. 如果不出意外，到此一个简单的监控系统就可以开始工作了，可以在Go的控制台上检查sensu客户端的工作状态，目前应该只有一些简单的心跳检查的log可以看，像是这：

    ![sensu 控制台](http://7xsfv0.com1.z0.glb.clouddn.com//clipboard_tmp_20160518_103153_849000.jpg)

我是在虚拟机上装的ubuntu，go环境装在了开发机上，虚拟机直接获取的内网地址，如果需要也可以全部部署在一台服务器上。

后面就开始研究一下sensu到底能监控点儿啥

今儿就这样儿了。

Reference：

1. [Sensu入门文档](https://segmentfault.com/a/1190000002386457)
2. [Sensu Installation Guide](https://sensuapp.org/docs/latest/installation-guide)
3. [How To Configure Sensu Monitoring, RabbitMQ, and Redis on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-configure-sensu-monitoring-rabbitmq-and-redis-on-ubuntu-14-04)
4. [Uchiwa Getting Started](http://docs.uchiwa.io/en/latest/getting-started/)