---
title: CentOS源码安装python
date: 2019-12-15 17:32:27
tags: [CentOS,Python]
---

一般来说直接源码编译就好了，在一些特定的环境下，比如最小化的CentOS版本中缺少编译以及所需要的依赖会不能进行编译。通过下面进行安装所需要的包。

```shell
yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel
```

然后再[Python官网](https://www.python.org/downloads/source/)下载所需要的Python版本源码。

比如下载Python3.8然后进行编译安装。

```shell
wget 'https://www.python.org/ftp/python/3.8.0/Python-3.8.0.tgz'
tar xf Python-3.8.0.tgz
cd Python-3.8.0
./configure
make&&make install
```

如上就已经编译安装完成了， 接下来进行配置来让python生成快捷方式。

```shell
ln -s /usr/local/bin/python3 /usr/bin/python3
ln -s /usr/local/bin/pip3 /usr/bin/pip3
```

以上就已经搞好了，可以直接使用`python3`和`pip3`来进行操作。

`python3`中加入了可以自带生成虚拟环境的功能，不再像`python2`一样需要单独安装`virtualenv`。

```shell
python3 -m venv 自定义环境名
source 自定义环境名/bin/activate
```

就可以切换到虚拟环境中了。

而再`pip >= 10.0.0`的版本中，可以简单的配置pip源。

```shell
pip install pip -U
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

以上是[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)给出的最新换源方式。

更换合适的源是开始python开发的第一步。