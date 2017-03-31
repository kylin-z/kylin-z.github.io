---
title: mongodb(mongoose)使用小结
date: 2017-03-17 19:40:54
tags: [mongodb,笔记]
categories: [mongodb]
---


## 文档结构

```javascript
{
	"origin":{
		"home_name":"伯乐在线",
		"home":"http://web.jobbole.com/",
		"link":"http://web.jobbole.com/90719/",
		"id":"90719"
	},
	"uid":8140,
	"title":"我理解中的“大前端”/“大无线”",
	"article_info":{
		"content":'<div class="entry">↵↵        ↵		<div>↵...'
		"categorys":["基础技术"],
		"copyright":{
			"link":"http://f2e.souche.com/blog/da-qian-duan-wo-de-li-jie/",
			"name":"大搜车无线团队"
		},
		"tags":["前端"],
		"post_date":1489161600,
		"is_filtered":false,
		"is_unique":false
	},
	"insert_time":1489750591.488951
}
```

## 根据某字段查询

### 根据`uid`字段查询

```js
Article.find({'uid':2})
```

### 根据`orgin`对象中的`home`字段查询，使用`.`操作符

```js
Article.find({'origin.home':'http://web.jobbole.com/'})
```

## 指定输出文档显示的字段

<p class="tip">前端渲染列表时，查询如示例文档这样的带有大段html文本的数据会比较慢，这时可以将UI渲染用不到的字段过滤</p>

```js
Article.find({'uid':2},{_id: 0, 'article_info.content': 0})
// 不输出_id与article_info下的content字段 0表示删除 1表示保留
```

<!-- more -->

## 排序

```js
Article.find({'uid':2},{_id: 0, 'article_info.content': 0}).sort('insert_time',-1)
// sort(key,1) key 表示排序的字段 第二个参数 1表示升序 -1表示降序
```

## 分页

### 当前第`page`页，每页`limit`条

```js
Article.find({'uid':2},{_id: 0, 'article_info.content': 0}).sort('insert_time',-1).skip((page - 1) * 10).limit(limit)
// skip(offset)表示当前偏移量 ，limit(limit)返回limit条数据
```

## `aggregate`聚合

[文档](https://docs.mongodb.com/manual/reference/method/db.collection.aggregate/#db.collection.aggregate)

<p class="tip">数据量较大是，使用`sort()`排序会报如下错误，此时可以使用`aggregate()`方法</p>

```
MongoError: Executor error during find command: OperationFailed: Sort operation used more than the maximum 33554432 bytes of RAM. Add an index, or specify a smaller limit.
```

> 原因是sort()是在内存中排序，最大允许占用32MB内存 (32MB in-memory sort limit)。

### 用`aggregate`实现上述功能

```js
Article.aggregate([
      {$sort:{'article_info.post_date': -1}},
      {$project: { _id: 0, 'article_info.content': 0}},
      {$skip:(page - 1) * 10},
      {$limit:limit}
    ]).allowDiskUse(true)
```

<p class="tip">不加allowDiskUse：true 可能会报如下error</p>

```
 MongoError: Sort exceeded memory limit of 104857600 bytes, but did not opt in to external sorting. Aborting operation. Pass allowDiskUse:true to opt in.
```

### 疑问

- 据我观察 方法一似乎比方法二快那么一丢丢，不知道是不是错觉

```js
// 方法一
var articles = await Article.aggregate([
      {$sort:{'article_info.post_date': -1}},
      {$project: { _id: 0, 'article_info.content': 0}}
    ]).allowDiskUse(true).skip((page - 1) * 10).limit(limit);

```

```js
// 方法二
var articles = await Article.aggregate([
	   {$sort:{'article_info.post_date': -1}},
	   {$project: { _id: 0, 'article_info.content': 0}},
	   {$skip:(page - 1) * 10},
	   {$limit:limit}
	 ]).allowDiskUse(true);

```


## 导出到excel(csv)

[参考官网](https://docs.mongodb.com/manual/reference/program/mongoexport/)
[参考](http://www.jb51.net/article/52498.htm)

```bash

mongoexport -h [IP]:[port] -d [db] -c [collection] -u [user] -p [password] --type=csv -f [field1[,field2,field3,...]] > [filename.csv]

mongoexport -h 115.29.29.113:27017 -d test -c test -u test -p test --type=csv -f "code","title","url","budget","date","organization" > filename.csv
```
