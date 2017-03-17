---
title: python折腾过程中遇到的坑全纪录
date: 2017-03-06 14:04:58
tags: [python,坑,mac]
categories: [python,笔记]
---
## Scrapy的依赖库Six与系统依赖库Six发生了冲突

<p class="tip">这应该是一个`mac`坑</p>

### error
```bash
DEPRECATION: Uninstalling a distutils installed project (six) has been deprecated and will be removed in a future version. This is due to the fact that uninstalling a distutils project will only partially uninstall the project.
    Uninstalling six-1.4.1:
		...
```

### 解决方法

参考[starkoverflow](http://stackoverflow.com/questions/31900008/oserror-errno-1-operation-not-permitted-when-installing-scrapy-in-osx-10-11)

- 使用virtualenv创建一个隔离的python环境
- 使用`--ignore-installed命令`

```bash
sudo pip install libName --upgrade --ignore-installed six
```

## Scrapy命令使用时报错

### error
```bash
ImportError: cannot import name xmlrpc_client
```

<p class="tip">这应该是一个`mac`坑</p>

### 方法一：重装`six`

```bash
sudo rm -rf /Library/Python/2.7/site-packages/six*
```

```bash
sudo rm -rf /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/six*
```
<!-- more -->

```bash
sudo pip install six
```

<p class="tip">在`EI Capitan`系统上，这个方法会失败，执行第二行时报错如下:</p>

```bash
Operation not permitted” error
# six1.4是预装的，无法删除
```

### 方法二：`.bash_profile`中添加

```bash
export PYTHONPATH=$PYTHONPATH:/Library/Python/2.7/site-packages
```
<p class="tip">亲测不好使</p>

### 方法三：用virtualenv创建隔离的python环境(mac可以使用virtualenvwrapper)

最后使用这个姿势解决了

- 使用方式

```bash
pip install virutalenv
```

```bash
virtualenv [虚拟环境名称]
# 创建虚拟环境
# 若不想使用系统包可以加一个参数
virtualenv --no-site-packages [虚拟环境名称]
# 如
virtualenv --no-site-packages py2_7
```


```bash
# 激活环境
cd py2_7
source ./bin/activate
```
此时，就进入了一个隔离的环境，安装，卸载和使用模块，都会在py2_7环境中，而不会影响系统和其他环境。

重新再环境中安装`scrapy`并使用

```bash
# 退出环境
deactive
```

<p class="tip">如果还不好使就重新安装系统中的python并卸载scrapy,再进入环境中安装scrapy</p>


## 使用scrapy命令提示缺少zope包

### error

```bash
No module named zope.interface
```

### 解决方式

- 下载`zope.interface`并解压
- 定位到zope文件夹，使用python命令安装

```bash
wget http://pypi.python.org/packages/source/z/zope.interface/zope.interface-4.0.1.tar.gz
tar xvf zope.interface-4.0.1.tar.gz
cd zope.interface-4.0.1
python setup.py install
```
