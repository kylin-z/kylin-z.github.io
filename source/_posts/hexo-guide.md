---
title: hexo个人博客搭建记录
date: 2016-02-28 18:15:15
tags: [hexo,github,node]
categories: hexo
---
1.安装git

2.安装node

3.安装hexo

```
npm install hexo-cli -g
```
4.创建一个放置博客的文件夹如：`H:\hexo\kylin`,最好不要使用中文路径,进入文件夹`H:\hexo\kylin`,右键选择git bash(重要),执行以下命令,Hexo会自动在该文件夹下下载搭建网站所需的所有文件

```
hexo init
```
5.安装依赖包
```
npm install 
```
6.在`H:\hexo\kylin`中执行以下命令
```
$ hexo g
$ hexo s
```
`hexo clean`删除`g`生成的public文件夹

`hexo g`类似于fis的release 将博客的md文件生成html到相应的产出目录`public`

`hexo s`启动本地服务器,打开`localhost:4000`即可看到已经生成的本地博客

`hexo d`推送到github

注意:若是使用过程中出现以下错误

```
ERROR Deployer not found : github
```

则运行以下命令:
```
$ npm install hexo-deployer-git --save
$ hexo g
$ hexo d
```

7._config.yml文件设置

未完待续。。。



