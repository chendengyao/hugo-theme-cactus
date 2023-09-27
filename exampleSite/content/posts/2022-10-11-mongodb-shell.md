---
title: MongoDB常用shell命令(增删改查)
date: 2022-10-17 13:38:00
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
MongoDB v3.6.23
```
## 进入mongo Shell
安装MongoDB会默认安装mongo shell.
```bash
# 进入mongo shell，不加参数默认localhost和27107端口
mongo

# 指定的ip和端口
mongo 127.0.0.1:27017

# 指定ip和端口,并进入指定的database
mongo 127.0.0.1:27017/dbName
```

## reference
+ [MongoDB 3.6官方文档](https://www.mongodb.com/docs/v3.6/) https://www.mongodb.com/docs/v3.6/