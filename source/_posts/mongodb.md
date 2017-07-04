---
title: 使用mongo shell命令行工具在指定数据库下新建用户
date: 2017-03-10 20:37:08
tags: [mongodb]
categories: mongodb
---
## 在指定数据库下新建用户

### 输入mongo命令

```bash
root@iZm5e3ljqlvdsruj2dkr7pZ:~# mongo
MongoDB shell version v3.4.2
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.4.2
>
```

### 先在admin数据库完成校验

```bash
> use admin
switched to db admin
> db.auth('xx','xxx')
1
```

### 切换到想要新建用户的数据库

```bash
> use test
switched to db test
```

### 使用`db.createUser()`新建用户,参考[文档](https://docs.mongodb.com/manual/reference/method/db.createUser/)

```bash
> db.createUser({
... ... user:"root",
... ... pwd:"root",
... ... roles:[{
... ... role:'readWrite',db:'test'
... ... }]
... ... })
Successfully added user: {
	"user" : "root",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "test"
		}
	]
}
```
