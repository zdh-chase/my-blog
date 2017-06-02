---
title: 初探mongodb（一）
tags:
     - mongodb
     - node
     - express
     - mongoose
     - body-parser
     - robomongo
---
配置：

1.nodejs（官网地址：<a href="https://nodejs.org/en/" target="_blank">https://nodejs.org/en/</a>下载安装）
2.express，基于nodejs的开发框架-<a href="http://www.expressjs.com.cn/" target="_blank">传送门</a>
3.mongodb
4.mongoose

<!--more-->

## 1.mongodb 安装配置

进入mongodb官网下载安装，附上<a href="https://www.mongodb.com/download-center?jmp=nav#community" target="_blank">传送门</a>，安装完成后的目录文件：
<img src="/img/img5.png" />
<p style="color:red">logs这个目录是后面手动新建的文件夹，用来存放日志文件的。</p>

## 2.修改环境变量

mongodb安装完成后，将mongdb的文件目录路径添加至系统变量path下
<img src="/img/img6.png" />

## 3.创建数据库目录

数据库目录不会自动创建，需要手动创建，这边我手动创建在F:\data\db


## 4.指定数据库存放目录及日志

打开cmd，输入：
``` bash
mongod --dbpath "F:\data\db" --logpath "F:\mongodb\logs\mongod.log"
```

## 5.安装mongodb为windows服务

通过命令启动mongo服务太麻烦，为mongodb注册系统服务，以管理员身份打开cmd，输入
``` bash
mongod --install --dbpath "F:\data\db" --logpath "F:\mongodb\logs\mongod.log"
```
更多设置
``` bash
mongod --bind_ip yourIPadress --logpath "F:\mongodb\logs\mongod.log" --logappend --dbpath "D:\data\db" --port yourPortNumber --serviceName "YourServiceName" --serviceDisplayName "YourServiceName" --install
```
参数 描述
--bind_ip 绑定服务IP，若绑定127.0.0.1，则只能本机访问，不指定默认本地所有IP
--logpath 指定MongoDB日志文件，注意是指定文件不是目录
--logappend 使用追加的方式写日志
--dbpath 指定数据库路径
--port 指定服务端口号，默认端口27017
--serviceName 指定服务名称
--serviceDisplayName 指定服务名称，有多个mongodb服务时执行。
--install 指定作为一个Windows服务安装。


## 6.mongo服务指令

添加完window服务后，以管理员身份打开cmd，启动服务输入命令：
``` bash
net start MongoDB
```
关闭服务命令：
``` bash
net stop MongoDB
```
<img src="/img/img7.png" />

## 7.报错

如果报错“MongoDB 服务正在启动 .MongoDB 服务无法启动。发生服务特定错误: 100.”，则前去db目录下的mongod.lock和storage.bson文件删掉，以管理员身份运行命令行然后重新启动服务即可。

over