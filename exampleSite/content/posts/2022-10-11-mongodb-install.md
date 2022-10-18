---
title: MongoDB安装使用
date: 2022-10-10 13:38:00
categories: blog
tags: 
- 数据库
- MongoDB
- NoSQL
keywords: MongoDB
---

环境:
``` 
Ubuntu 20.04.1 LTS (GNU/Linux 5.4.0-47-generic x86_64)
```
## 安装
MongoDB有社区版和企业版，社区版就行了。找到
[Ubuntu安装的官方文档](https://www.mongodb.com/docs/v3.6/tutorial/install-mongodb-on-ubuntu/)。
安装命令如下:
```bash 
# 添加公钥
wget -qO - https://www.mongodb.org/static/pgp/server-3.6.asc | sudo apt-key add -
# 添加软件源，不同的Ubuntu版本可能会不同，建议看官方文档
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list

sudo apt-get update
# 安装
sudo apt-get install -y mongodb-org
```
安装成功查看MongoDB版本：
```bash
root@iZuf6bl67fhppotxvfrmhdZ:~# mongo --version
MongoDB shell version v3.6.23
git version: d352e6a4764659e0d0350ce77279de3c1f243e5c
OpenSSL version: OpenSSL 1.1.1f  31 Mar 2020
allocator: tcmalloc
modules: none
build environment:
    distmod: ubuntu1804
    distarch: x86_64
    target_arch: x86_64
```
## 启动
MongoDB的配置文件在 `/etc/mongod.conf` 里面，先简单介绍一下：
```
# 官方的配置文档：
#   http://docs.mongodb.org/manual/reference/configuration-options/
# 存放数据的地方
storage:
  dbPath: /var/lib/mongodb
  journal:
    enabled: true
# 日志存放的地方
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log
# 运行的端口和允许访问的ip
net:
  port: 27017
  bindIp: 127.0.0.1
```
如果要修改运行的端口之类的可以先在配置文件里面修改之后再启动。
```bash
# 启动
sudo systemctl start mongod
# 查看运行状态
sudo systemctl status mongod
# 自启动
sudo systemctl enable mongod
# 停止运行
sudo systemctl stop mongod
# 重启
sudo systemctl restart mongod
```
如果启动失败的话可以查看日志 `/var/log/mongodb` 寻找相关解决方法。

## 连接和简单使用
MongoDB 也提供了[shell工具](https://www.mongodb.com/docs/v3.6/mongo/)来操作数据库：
``` bash
# 进入shell，默认本机27017端口
mongo
# 指定ip和端口连接MongoDB
mongo --host 127.0.0.1:27017
# 显示当前使用的数据库，默认的数据库为test
db
# 显示全部数据库
show dbs
# 切换当前操作的数据库，没有则会直接创建
use dbName
# 在数据库插入一条数据,这里的user在MongoDB称之为Collection相当于Mysql表的概念，即在user表中添加一条数据
db.user.insertOne({name:"cdy",sex:"male",age:23,food:["apple","banana"]})
# 查看Collection中所有的数据
db.user.find()
# 列出所有的Collection
show collections
```

## reference
+ [MongoDB 3.6官方文档](https://www.mongodb.com/docs/v3.6/) https://www.mongodb.com/docs/v3.6/